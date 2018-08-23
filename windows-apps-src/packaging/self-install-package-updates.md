---
author: mcleanbyron
ms.assetid: 414ACC73-2A72-465C-BD15-1B51CB2334F2
title: Скачивание и установка обновлений пакетов из Store
description: Узнайте, как пометить пакеты как обязательные на информационной панели Центра разработки и написать код в приложении для загрузки и установки обновлений пакетов.
ms.author: mcleans
ms.date: 04/04/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 39b3ba05b06b6d484804999a935accc7223b5c60
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2814536"
---
# <a name="download-and-install-package-updates-from-the-store"></a>Скачивание и установка обновлений пакетов из Store

Начиная с Windows 10 версии 1607, можно использовать методы класса [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), чтобы программным способом проверить наличие обновлений пакета для текущего приложения в Microsoft Store и загрузить и установить обновленные пакеты. Можно также запрашивать пакеты, которые были помечены как обязательные на информационной панели Центра разработки для Windows, и отключать функциональные возможности приложения, пока обязательное обновление не будет установлено.

Дополнительные методы [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext), представленные в Windows 10 версии 1803, позволяют скачивать и устанавливать обновления пакетов автоматически (не отображая пользовательский интерфейс уведомления пользователю), удалять [дополнительный пакет](optional-packages.md) и получать информацию о пакетах в очереди на загрузку и установку для вашего приложения.

Эти функции помогают автоматически поддерживать базу пользователей в актуальном состоянии, используя последние версии приложения, дополнительные пакеты и сопутствующие услуги в Store.

## <a name="download-and-install-package-updates-with-the-users-permission"></a>Загрузка и установка обновлений пакета с разрешения пользователя

В этом примере кода показано, как использовать метод [GetAppAndOptionalStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getappandoptionalstorepackageupdatesasync), чтобы обнаружить все доступные обновления пакетов в Store, а затем вызвать метод [RequestDownloadAndInstallStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestdownloadandinstallstorepackageupdatesasync) для загрузки и установки обновлений. При использовании этого метода для загрузки и установки обновлений ОС отображает диалоговое окно с запросом разрешения пользователя, прежде чем загружать обновления.

> [!NOTE]
> Эти методы поддерживают обязательные и [необязательные пакеты](optional-packages.md) для вашего приложения. Необязательные пакеты полезны для надстроек загружаемого контента (DLC), так как позволяют поделить большое приложение при наличии ограничений на размер или доставить любое дополнительное содержимое отдельно от базового приложения. Для получения разрешения на отправку приложения, использующего дополнительные пакеты (включая надстройки DLC) в Store, см. раздел [Поддержка разработчиков для Windows](https://developer.microsoft.com/windows/support).

В коде из этого примера предполагается следующее:

* Код выполняется в контексте [страницы](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx).
* **Страница** содержит панель [ProgressBar](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressbar.aspx) с именем ```downloadProgressBar``` для предоставления состояния операции загрузки.
* Файл кода имеет заявление **using** для пространств имен **Windows.Services.Store**, **Windows.Threading.Tasks** и **Windows.UI.Popups**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Для [многопользовательского приложения](https://msdn.microsoft.com/windows/uwp/xbox-apps/multi-user-applications) используйте метод [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.User), чтобы получить объект **StoreContext**, а не метод [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.GetDefault).

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

> [!NOTE]
> Чтобы только загрузить доступные обновления пакетов (не устанавливая их), воспользуйтесь методом [RequestDownloadStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestdownloadstorepackageupdatesasync).

### <a name="display-download-and-install-progress-info"></a>Отображение информации о ходе загрузки и установки

При вызове метода [RequestDownloadStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestdownloadstorepackageupdatesasync) или [RequestDownloadAndInstallStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestdownloadandinstallstorepackageupdatesasync) можно назначить обработчик [Progress](https://docs.microsoft.com/uwp/api/windows.foundation.iasyncoperationwithprogress-2.progress), который будет вызываться один раз для каждого шага при скачивании (или скачивании и установке) для каждого пакета в этом запросе. Обработчик получает объект [StorePackageUpdateStatus](https://docs.microsoft.com/uwp/api/windows.services.store.storepackageupdatestatus), который предоставляет сведения о пакете обновления, создавшем уведомление о ходе выполнения. В предыдущем примере используется поле **PackageDownloadProgress** объекта **StorePackageUpdateStatus** для отображения хода выполнения скачивания и установки.

Не забывайте, что при вызове **RequestDownloadAndInstallStorePackageUpdatesAsync** для скачивания и установки обновлений пакета в одной операции поле **PackageDownloadProgress** увеличивается с 0.0 до 0.8 во время скачивания для пакета, а затем увеличивается с 0.8 до 1.0 во время установки. Поэтому при сопоставлении процента, показанного в специальном интерфейсе хода выполнения для значения поля **PackageDownloadProgress** ваш интерфейс будет отображать 80%, когда скачивание пакета закончится, а ОС отобразит диалоговое окно установки. Если вы хотите, чтобы специальный интерфейс хода выполнения отображал 100%, когда пакет будет скачан и готов к установке, вы можете изменить код и назначить 100% интерфейсу хода выполнения, когда значение поля **PackageDownloadProgress** достигнет 0.8.

## <a name="download-and-install-package-updates-silently"></a>Скачивание и установка всех обновлений пакетов автоматически

Начиная с Windows 10 версии 1803 можно использовать методы [TrySilentDownloadStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.trysilentdownloadstorepackageupdatesasync) и [TrySilentDownloadAndInstallStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.trysilentdownloadandinstallstorepackageupdatesasync) для автоматической загрузки и установки обновлений пакетов, не отображая пользовательский интерфейс уведомления пользователю. Эта операция завершится успешно, только если пользователь включил параметр **Обновлять приложения автоматически** в Store и если пользователь не находится в сети с лимитным тарифным планом. Прежде чем вызывать эти методы, нужно проверить свойство [CanSilentlyDownloadStorePackageUpdates](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.cansilentlydownloadstorepackageupdates), чтобы определить, выполняются ли эти условия в настоящее время.

В этом примере кода показано, как использовать метод [GetAppAndOptionalStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getappandoptionalstorepackageupdatesasync), чтобы обнаружить все доступные обновления пакетов, а затем вызвать методы [TrySilentDownloadStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.trysilentdownloadstorepackageupdatesasync) и [TrySilentDownloadAndInstallStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.trysilentdownloadandinstallstorepackageupdatesasync) для автоматической загрузки и установки обновлений.

В коде из этого примера предполагается следующее:
* В файле кода используются оператор **using** для пространств имен **Windows.Services.Store** и **System.Threading.Tasks**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Для [многопользовательского приложения](https://msdn.microsoft.com/windows/uwp/xbox-apps/multi-user-applications) используйте метод [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.User), чтобы получить объект **StoreContext**, а не метод [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.GetDefault).

> [!NOTE]
> Методы **IsNowAGoodTimeToRestartApp**, **RetryDownloadAndInstallLater** и **RetryInstallLater**, вызываемые в коде в этом примере, являются методами-заполнителями, которые реализуются по мере надобности в соответствии со структурой вашего приложения.

```csharp
private StoreContext context = null;

public async Task DownloadAndInstallAllUpdatesInBackgroundAsync()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Get the updates that are available.
    IReadOnlyList<StorePackageUpdate> storePackageUpdates =
        await context.GetAppAndOptionalStorePackageUpdatesAsync();

    if (storePackageUpdates.Count > 0)
    {

        if (!context.CanSilentlyDownloadStorePackageUpdates)
        {
            return;
        }

        // Start the silent downloads and wait for the downloads to complete.
        StorePackageUpdateResult downloadResult =
            await context.TrySilentDownloadStorePackageUpdatesAsync(storePackageUpdates);

        switch (downloadResult.OverallState)
        {
            case StorePackageUpdateState.Completed:
                // The download has completed successfully. At this point, confirm whether your app
                // can restart now and then install the updates (for example, you might only install
                // packages silently if your app has been idle for a certain period of time). The
                // IsNowAGoodTimeToRestartApp method is not implemented in this example, you should
                // implement it as needed for your own app.
                if (IsNowAGoodTimeToRestartApp())
                {
                    await InstallUpdate(storePackageUpdates);
                }
                else
                {
                    // Retry/reschedule the installation later. The RetryInstallLater method is not  
                    // implemented in this example, you should implement it as needed for your own app.
                    RetryInstallLater();
                    return;
                }
                break;
            // If the user cancelled the download or you can't perform the download for some other
            // reason (for example, Wi-Fi might have been turned off and the device is now on
            // a metered network) try again later. The RetryDownloadAndInstallLater method is not  
            // implemented in this example, you should implement it as needed for your own app.
            case StorePackageUpdateState.Canceled:
            case StorePackageUpdateState.ErrorLowBattery:
            case StorePackageUpdateState.ErrorWiFiRecommended:
            case StorePackageUpdateState.ErrorWiFiRequired:
            case StorePackageUpdateState.OtherError:
                RetryDownloadAndInstallLater();
                return;
            default:
                break;
        }
    }
}

private async Task InstallUpdate(IReadOnlyList<StorePackageUpdate> storePackageUpdates)
{
    // Start the silent installation of the packages. Because the packages have already
    // been downloaded in the previous method, the following line of code just installs
    // the downloaded packages.
    StorePackageUpdateResult downloadResult =
        await context.TrySilentDownloadAndInstallStorePackageUpdatesAsync(storePackageUpdates);

    switch (downloadResult.OverallState)
    {
        // If the user cancelled the installation or you can't perform the installation  
        // for some other reason, try again later. The RetryInstallLater method is not  
        // implemented in this example, you should implement it as needed for your own app.
        case StorePackageUpdateState.Canceled:
        case StorePackageUpdateState.ErrorLowBattery:
        case StorePackageUpdateState.OtherError:
            RetryInstallLater();
            return;
        default:
            break;
    }
}
```

## <a name="mandatory-package-updates"></a>Обязательные обновления пакета

При создании отправки пакета для приложения, которое предназначено для Windows 10 версии 1607 или выше, можно [пометить пакет как обязательный](../publish/upload-app-packages.md#mandatory-update) и указать дату и время, когда он становится обязательным. Если это свойство задано и приложение обнаружит, что обновление пакета доступно, приложение может определить, является ли пакет обновлений одинаковым, и изменить его поведение до тех пор, пока обновление не будет установлено (например, приложение может отключить те или иные компоненты).

> [!NOTE]
> Обязательный статус обновления пакета не реализуется Microsoft принудительно, и ОС не предоставляет пользовательский интерфейс, указывающий пользователям, что обязательное обновление приложения необходимо установить. Разработчики должны использовать обязательную настройку, чтобы принудительно реализовать обязательные обновления приложений в своем коде.  

Чтобы пометить отправку пакета как обязательную, выполните следующие действия:

1. Выполните вход на [информационную панель Центра разработки](https://dev.windows.com/overview) и перейдите на страницу обзора для вашего приложения.
2. Щелкните имя отправки, содержащий пакет обновления, которые вы хотите сделать обязательным.
3. Перейдите на страницу **Пакеты** для этой отправки. В нижней части страницы выберите **Сделать это обновление обязательным** и выберите день и время, когда пакет станет обязательным. Этот параметр применяется ко всем пакетам UWP в отправке.

Подробнее об этом см. в разделе [Передача пакетов приложения](../publish/upload-app-packages.md).

> [!NOTE]
> Если вы создаете [тестовый пакет](../publish/package-flights.md), то можете отметить пакеты как обязательные с помощью аналогичного пользовательского интерфейса на странице **Packages** для тестируемой возможности. В этом случае обязательное обновление пакета применяется только к клиентам, которые являются частью тестовой группы.

### <a name="code-example-for-mandatory-packages"></a>Пример кода для обязательных пакетов

В следующем примере кода показано, как определить, являются ли пакеты обновлений обязательными. Как правило, если обязательное обновление пакета не скачивается или не устанавливается успешно, необходимо правильно понизить версию приложения для пользователя.

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

## <a name="uninstall-optional-packages"></a>Удаление дополнительных пакетов

Начиная с Windows 10 версии 1803 можно использовать методы [RequestUninstallStorePackageAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestuninstallstorepackageasync) или [RequestUninstallStorePackageByStoreIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestuninstallstorepackagebystoreidasync), чтобы удалить [дополнительный пакет](optional-packages.md) (включая пакет DLC) для текущего приложения. Например, если у вас есть приложение с содержимым, которое установлено с помощью дополнительных пакетов, можно предоставить пользовательский интерфейс, который позволяет удалять дополнительные пакеты, чтобы освободить место на диске.

В следующем примере кода показан вызов метода [RequestUninstallStorePackageAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestuninstallstorepackageasync). В коде из этого примера предполагается следующее:
* В файле кода используются оператор **using** для пространств имен **Windows.Services.Store** и **System.Threading.Tasks**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Для [многопользовательского приложения](https://msdn.microsoft.com/windows/uwp/xbox-apps/multi-user-applications) используйте метод [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.User), чтобы получить объект **StoreContext**, а не метод [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.GetDefault).

```csharp
public async Task UninstallPackage(Windows.ApplicationModel.Package package)
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    var storeContext = StoreContext.GetDefault();
    IAsyncOperation<StoreUninstallStorePackageResult> uninstallOperation =
        storeContext.RequestUninstallStorePackageAsync(package);

    // At this point, you can update your app UI to show that the package
    // is installing.

    uninstallOperation.Completed += (asyncInfo, status) =>
    {
        StoreUninstallStorePackageResult result = uninstallOperation.GetResults();
        switch (result.Status)
        {
            case StoreUninstallStorePackageStatus.Succeeded:
                {
                    // Update your app UI to show the package as uninstalled.
                    break;
                }
            default:
                {
                    // Update your app UI to show that the package uninstall failed.
                    break;
                }
        }
    };
}
```

## <a name="get-download-queue-info"></a>Получение сведений об очереди на загрузку

Начиная с Windows 10 версии 1803 можно использовать методы [GetAssociatedStoreQueueItemsAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getassociatedstorequeueitemsasync) и [GetStoreQueueItemsAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getstorequeueitemsasync) для получения информации о пакетах, которые сейчас находятся в очереди на загрузку и установку из Store. Эти методы полезны, если ваше приложение или игра поддерживает большие дополнительные пакеты (включая DLC), загрузка и установка которых может занять несколько часов или дней, и вы хотите эффективно обработать сценарий, когда клиент закрывает ваше приложение или игру до завершения загрузки или установки. Когда пользователь снова запустит приложение или игру, код может использовать эти методы, чтобы получить информацию о состоянии пакетов, которые до сих пор находятся в очереди на загрузку и установку, чтобы пользователю можно было показать статус каждого пакета.

В следующем примере кода показано, как вызывать метод [GetAssociatedStoreQueueItemsAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getassociatedstorequeueitemsasync), чтобы получить список текущих обновлений пакета для текущего приложения и извлечь сведения о статусе для каждого пакета. В коде из этого примера предполагается следующее:
* В файле кода используются оператор **using** для пространств имен **Windows.Services.Store** и **System.Threading.Tasks**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Для [многопользовательского приложения](https://msdn.microsoft.com/windows/uwp/xbox-apps/multi-user-applications) используйте метод [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.User), чтобы получить объект **StoreContext**, а не метод [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.GetDefault).

> [!NOTE]
> Методы **MarkUpdateInProgressInUI**, **RemoveItemFromUI**, **MarkInstallCompleteInUI**, **MarkInstallErrorInUI** и **MarkInstallPausedInUI**, вызываемые в коде в этом примере,— это методы-местозаполнители, которые предназначены для реализации по мере надобности в соответствии с конструкцией вашего приложения.

```csharp
private StoreContext context = null;

private async Task GetQueuedInstallItemsAndBuildInitialStoreUI()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Get the Store packages in the install queue.
    IReadOnlyList<StoreQueueItem> storeUpdateItems = await context.GetAssociatedStoreQueueItemsAsync();

    foreach (StoreQueueItem storeItem in storeUpdateItems)
    {
        // In this example we only care about package updates.
        if (storeItem.InstallKind != StoreQueueItemKind.Update)
            continue;

        StoreQueueItemStatus currentStatus = storeItem.GetCurrentStatus();
        StoreQueueItemState installState = currentStatus.PackageInstallState;
        StoreQueueItemExtendedState extendedInstallState =
            currentStatus.PackageInstallExtendedState;

        // Handle the StatusChanged event to display current status to the customer.
        storeItem.StatusChanged += StoreItem_StatusChanged;

        switch (installState)
        {
            // Download and install are still in progress, so update the status for this  
            // item and provide the extended state info. The following methods are not
            // implemented in this example; you should implement them as needed for your
            // app's UI.
            case StoreQueueItemState.Active:
                MarkUpdateInProgressInUI(storeItem, extendedInstallState);
                break;
            case StoreQueueItemState.Canceled:
                RemoveItemFromUI(storeItem);
                break;
            case StoreQueueItemState.Completed:
                MarkInstallCompleteInUI(storeItem);
                break;
            case StoreQueueItemState.Error:
                MarkInstallErrorInUI(storeItem);
                break;
            case StoreQueueItemState.Paused:
                MarkInstallPausedInUI(storeItem, installState, extendedInstallState);
                break;
        }
    }
}

private void StoreItem_StatusChanged(StoreQueueItem sender, object args)
{
    StoreQueueItemStatus currentStatus = sender.GetCurrentStatus();
    StoreQueueItemState installState = currentStatus.PackageInstallState;
    StoreQueueItemExtendedState extendedInstallState = currentStatus.PackageInstallExtendedState;

    switch (installState)
    {
        // Download and install are still in progress, so update the status for this  
        // item and provide the extended state info. The following methods are not
        // implemented in this example; you should implement them as needed for your
        // app's UI.
        case StoreQueueItemState.Active:
            MarkUpdateInProgressInUI(sender, extendedInstallState);
            break;
        case StoreQueueItemState.Canceled:
            RemoveItemFromUI(sender);
            break;
        case StoreQueueItemState.Completed:
            MarkInstallCompleteInUI(sender);
            break;
        case StoreQueueItemState.Error:
            MarkInstallErrorInUI(sender);
            break;
        case StoreQueueItemState.Paused:
            MarkInstallPausedInUI(sender, installState, extendedInstallState);
            break;
    }
}
```

## <a name="related-topics"></a>Статьи по теме

* [Разработка дополнительных пакетов и связанных наборов](optional-packages.md)

---
Description: 'В этом разделе показаны примеры задач программирования, необходимых для реализации некоторых наиболее распространенных сценариев защиты корпоративных данных (EDP) при их передаче.'
MS-HAID: 'dev\_app\_to\_app.use\_edp\_to\_protect\_enterprise\_data\_transferred\_between\_apps'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: Использование EDP для защиты корпоративных данных, передаваемых между приложениями
---

# Использование EDP для защиты корпоративных данных, передаваемых между приложениями

__Примечание__ Политику защиты корпоративных данных (EDP) нельзя применить в Windows 10 версии 1511 (сборка 10586) или более ранних версий.

В этом разделе показаны примеры задач программирования, необходимых для реализации некоторых наиболее распространенных сценариев защиты корпоративных данных (EDP) при их передаче. Полное описание взаимосвязи EDP с файлами, потоками, буфером обмена, сетями, фоновыми задачами и защитой данных в режиме блокировки для разработчиков см. в разделе [Защита корпоративных данных (EDP)](../enterprise/edp-hub.md).

**Примечание**  В [примере защиты корпоративных данных](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409) проиллюстрированы многие сценарии файлов, обсуждаемые в этом разделе.

## Необходимые условия


-   **Настройка для EDP**

    См. раздел [Настройка компьютера для EDP](../enterprise/edp-hub.md#set-up-your-computer-for-EDP).

-   **Создание корпоративно-грамотного приложения**

    Приложение, которое автономно гарантирует, что корпоративные данные останутся под управлением предприятия, называют корпоративно-грамотным приложением. Грамотное приложение более мощное, функциональное, гибкое и надежное, чем неграмотное. Ваше приложение сообщает системе, что оно грамотное, объявляя ограниченную возможность **enterpriseDataPolicy**. Но чтобы приложение было грамотным, недостаточно просто объявить соответствующую возможность. Подробнее: [Корпоративно-грамотные приложения](../enterprise/edp-hub.md#enterprise-enlightened-apps).

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C\# или Visual Basic см. в статье [Вызов асинхронных API в C\# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

## Простой источник буфера обмена


В этом сценарии ваше приложение является приложением типа «блокнот», способным обрабатывать как личные, так и корпоративные файлы. В данном случае вашему приложению не нужны какие-либо изменения логики копирования-вставки. Оно просто должно вызывать [**ProtectionPolicyManager.TryApplyProcessUIPolicy**](https://msdn.microsoft.com/library/windows/apps/dn705791) каждый раз, когда пользователь открывает и отображает содержимое корпоративного документа. Когда содержимое отобразится в пользовательском интерфейсе приложения, пользователь может скопировать его, чтобы затем вставить в другое приложение. Поэтому определение политики пользовательского интерфейса является важным моментом. Таким образом, операционная система может применить заданную в текущий момент политику к операции вставки, в том числе к тем, в которых задействованы защищенные данные. Аналогичным образом важно очищать политику пользовательского интерфейса, как только в ней отпадает необходимость. Это позволяет пользователю снова беспрепятственно копировать и вставлять личные данные. **TryApplyProcessUIPolicy** возвращает значение false, если его аргумент идентификатора не управляется политикой предприятия.

```CSharp
using Windows.Security.EnterpriseData;

...

private void OnFileLoaded(FileProtectionInfo fileProtectionInfo, string contentsOfFile)
{
    if (fileProtectionInfo.Status == FileProtectionStatus.Protected)
    {
        bool isIdentityManaged = ProtectionPolicyManager.TryApplyProcessUIPolicy
            (fileProtectionInfo.Identity);
        if (isIdentityManaged)
        {
            // UI policy is now in effect for the file's identity.
        }
        else
        {
            // Enterprise policy is not in effect, because the file&#39;s identity
            // is not managed. In this case, we have a file protected to an
            // unmanaged identity, which is not a valid situation.
            // We still have to call ClearProcessUIPolicy if we want to clear the policy.
            ProtectionPolicyManager.ClearProcessUIPolicy();
        }
    }
    else
    {
        // In case we applied the policy for the previous file, now we need to clear it.
        ProtectionPolicyManager.ClearProcessUIPolicy();
    }
    // Code goes here to display "contentsOfFile" in your UI. Ready for copy-paste...
}
```

## Простая цель буфера обмена


В этом сценарии ваше приложение является почтовым клиентом, который работает как с личными, так и с корпоративными учетными записями. Пользователь пытается вставить данные в ответное электронное письмо, используя свою личную учетную запись. В этом случае перед получением содержимого приложение должно просто вызвать [**DataPackage.RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706636). Если доступ уже имеется, этот метод сразу возвращает значение. Но если доступ отсутствует, метод вызывает диалоговое окно, которое запрашивает согласие пользователя. При получении согласия метод разблокирует пакет данных. Это дает пользователю возможность отменить ошибочную операцию вставки.

```CSharp
using Windows.ApplicationModel.DataTransfer;

...

private async void OnPasteSimple()
{
    DataPackageView dataPackageView = Clipboard.GetContent();
    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        // In case we don't already have acccess, request consent from the user
        // for us to access the clipboard data.
        await dataPackageView.RequestAccessAsync();

        try
        {
            string contentsOfClipboard = await dataPackageView.GetTextAsync();
            // Code goes here to insert the text into the email...
        }
        catch (Exception)
        {
            // Code goes here to handle the exception retrieving text from the clipboard.
        }
    }
    else
    {
        // The value on the clipboard is not in the text format.
    }
}
```

## Цель буфера обмена — нейтральный, пустой документ


В этом сценарии ваше приложение является текстовым редактором. После создания нового документа приложение считает его нейтральным (ни корпоративным, ни личным) до тех пор, пока он остается пустым. Пользователь вставляет корпоративные данные в этот нейтральный документ. Поскольку документ является нейтральным, мы можем просто переключить его состояние и сделать его корпоративным, не вызывая при этом [**DataPackage.RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706636) (так как отображение диалогового окна в данном случае не требуется). Поэтому, если данные защищены, мы просто переключаем документ в корпоративный режим и вставляем данные.

```CSharp
using Windows.ApplicationModel.DataTransfer;

...

private async void OnPasteWithApplyPolicy()
{
    DataPackageView dataPackageView = Clipboard.GetContent();
    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        // If the data has no enterprise identity, then we already have access.
        if (!string.IsNullOrEmpty(dataPackageView.Properties.EnterpriseId))
        {
            ProtectionPolicyEvaluationResult policyResult =
                await dataPackageView.RequestAccessAsync(dataPackageView.Properties.EnterpriseId);
            if (this.isNewEmptyDocument &amp;&amp;
                policyResult == ProtectionPolicyEvaluationResult.Allowed)
            {
                // If this is a new and empty document, and we're allowed to access
                // the data, then we can avoid popping the consent dialog.
                bool isIdentityManaged = ProtectionPolicyManager.TryApplyProcessUIPolicy(dataPackageView.Properties.EnterpriseId);
                // You can use "isIdentityManaged", but it's not necessary.
            }
            else
            {
                // In this case, we can't optimize the workflow, so we just
                // request consent from the user in this case.
                await dataPackageView.RequestAccessAsync();
            }
        }

        try
        {
            string contentsOfClipboard = await dataPackageView.GetTextAsync();
            // Code goes here to insert the text into the email...
        }
        catch (Exception)
        {
            // Code goes here to handle the exception retrieving text from the clipboard.
        }
    }
    else
    {
        // The value on the clipboard is not in the text format.
    }
}
```

## Источник буфера обмена с явным указанием корпоративного удостоверения


В этом сценарии ваше приложение является текстовым редактором. Оно использует фоновый поток, чтобы выполнять совершаемые пользователем операции копирования. Пользователь копирует какие-то данные из корпоративного файла и сразу переключается на личный файл. В этот момент глобальный контекст приложения становится личным. На этом этапе в связи с тем, что глобальное состояние изменилось и не должно использоваться, фоновый поток приложения должен явным образом указать буферу обмена, что входящие данные являются корпоративными. Этого можно достичь, установив свойство [**DataPackagePropertySet.EnterpriseId**](https://msdn.microsoft.com/library/windows/apps/dn913861).

```CSharp
using Windows.ApplicationModel.DataTransfer;

...

private void CopyToClipboard(string stringToCopy, string identity)
{
    // Copy the string to the clipboard.
    var dataPackage = new DataPackage();
    dataPackage.SetText(stringToCopy);
    dataPackage.Properties.EnterpriseId = identity;
    Clipboard.SetContent(dataPackage);
}
```

## Пометка определенного окна с помощью корпоративного удостоверения


В этом сценарии ваше приложение является текстовым редактором, обрабатывающим несколько документов в разных окнах. Некоторые из них являются корпоративными, а некоторые — личными. Приложению необходимо гарантировать, что данные, вставляемые в окно с личным документом, оценены правильно (то есть отклонены или одобрены, если это корпоративные данные) и что данные из окна корпоративного документа правильно помечены. Этого можно достичь, установив свойство [**ProtectionPolicyManager.Identity**](https://msdn.microsoft.com/library/windows/apps/dn705785).

```CSharp
using Windows.Security.EnterpriseData;

...

private void TagCurrentViewWithEnterpriseId(string identity)
{
    ProtectionPolicyManager.GetForCurrentView().Identity = identity;
}
```

## Пометка исходящего перетаскиваемого объекта с помощью корпоративного удостоверения


В вашем приложении имеется открытое личное окно, в которое были перетащены какие-то корпоративные материалы. Пользователь начинает перетаскивать часть этих материалов, и приложению необходимо пометить содержимое как корпоративное. Этот сценарий не задействует каких-либо новых API. В данном сценарии приложение задает свойство [**DataPackagePropertySet.EnterpriseId**](https://msdn.microsoft.com/library/windows/apps/dn913861) (см. раздел [Источник буфера обмена с явным указанием корпоративного удостоверения](#clipboard_source_explicit_id) выше).

## Запросы корпоративного удостоверения перетаскиваемого объекта


В вашем приложении открыт новый пустой документ, который является нейтральным, пока он пуст. Пользователь перетаскивает в документ определенное содержимое. Приложение должно определить корпоративное удостоверение объекта, чтобы соответствующим образом изменить состояние документа. В данном сценарии ваше приложение получит свойство **EnterpriseId** из пакета данных в результате чтения [**DataPackagePropertySet.EnterpriseId**](https://msdn.microsoft.com/library/windows/apps/dn913861) (см. раздел [Источник буфера обмена с явным указанием корпоративного удостоверения](#clipboard_source_explicit_id) выше).

## Приложение в качестве источника контракта отправки данных


Если приложение поддерживает контракт отправки данных, установите контекст корпоративного удостоверения в [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/br205873), как показано в следующем примере кода, чтобы настроить источник отправки.

**Примечание**  Этот пример кода зависит от того, установлено ли для текущего представления удостоверение в объекте диспетчера политики защиты (см. раздел [Пометка определенного окна с помощью корпоративного удостоверения](#tag_window_with_id)). В противном случае свойство [**ProtectionPolicyManager.Identity**](https://msdn.microsoft.com/library/windows/apps/dn705785) будет содержать пустую строку.



```CSharp
using Windows.ApplicationModel.DataTransfer;
using Windows.Security.EnterpriseData;

...

private void OnShareSourceOperation(object sender, RoutedEventArgs e)
{
    // Register the current page as a share source (or you could do this earlier in your app).
    DataTransferManager.GetForCurrentView().DataRequested += OnDataRequested;
    DataTransferManager.ShowShareUI();
}

private void OnDataRequested(DataTransferManager sender, DataRequestedEventArgs args)
{
    if (!string.IsNullOrEmpty(this.shareSourceContent))
    {
        var protectionPolicyManager = ProtectionPolicyManager.GetForCurrentView();
        DataPackage requestData = args.Request.Data;
        requestData.Properties.Title = this.shareSourceTitle;
        requestData.Properties.EnterpriseId = protectionPolicyManager.Identity;
        requestData.SetText(this.shareSourceContent);
    }
}
```

## Приложение как получатель контракта отправки данных


В этом примере кода снова показана наша политика, которая обеспечивает свободное переключение контекста для входящих данных до тех пор, пока рабочий файл пуст, а также позволяет избежать отображения всплывающих запросов пользовательского интерфейса там, где это возможно. Когда приложение получает данные как получатель контракта отправки данных, оно должно вызвать [**ProtectionPolicyManager.TryApplyProcessUIPolicy**](https://msdn.microsoft.com/library/windows/apps/dn705791), если не имеет содержимого. В противном случае оно должно вызвать [**DataPackage.RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706636). Пример кода показывает, как это происходит.

```CSharp
using Windows.ApplicationModel.DataTransfer;
using Windows.ApplicationModel.DataTransfer.ShareTarget;

...

string identity = "microsoft.com";

protected override async void OnShareTargetActivated(ShareTargetActivatedEventArgs args)
{
    ShareOperation shareOperation = args.ShareOperation;
    if (shareOperation.Data.Contains(StandardDataFormats.Text))
    {
        // If the data has no enterprise identity, then we already have access.
        if (!string.IsNullOrEmpty(shareOperation.Data.Properties.EnterpriseId))
        {
            ProtectionPolicyEvaluationResult protectionPolicyEvaluationResult =
                await ProtectionPolicyManager.RequestAccessAsync
                    (shareOperation.Data.Properties.EnterpriseId, identity);
            if (this.isNewEmptyDocument && protectionPolicyEvaluationResult ==
                ProtectionPolicyEvaluationResult.Allowed)
            {
                // If this is a new and empty document, and we&#39;re allowed to access
                // the data, then we can avoid popping the consent dialog.
                bool isIdentityManaged = ProtectionPolicyManager.TryApplyProcessUIPolicy
                    (shareOperation.Data.Properties.EnterpriseId);
                // You can use "isIdentityManaged", but it';s not necessary.
            }
            else
            {
                // In this case, we can't optimize the workflow, so we just
                // request consent from the user in this case.
                protectionPolicyEvaluationResult = await shareOperation.Data.RequestAccessAsync();
            }
        }

        try
        {
            // Get the text from the share operation...
            App.shareTargetContent = await shareOperation.Data.GetTextAsync();
        }
        catch (Exception)
        {
            // Code goes here to handle the exception retrieving text from the share operation.
        }
    }
    else
    {
        // The value in the share operation is not in the text format.
    }
}
```

## Пассивный запрос политики копирования и вставки


В этом сценарии приложение разрешает вставку, только когда данные находятся в буфере обмена. Для этой функции можно использовать метод [**ProtectionPolicyManager.CheckAccess**](https://msdn.microsoft.com/library/windows/apps/dn705783), позволяющий выполнять пассивную проверку политики.

**Примечание**  Этот пример кода зависит от того, установлено ли для текущего представления удостоверение в объекте диспетчера политики защиты (см. раздел [Пометка определенного окна с помощью корпоративного удостоверения](#tag_window_with_id)). В противном случае свойство [**ProtectionPolicyManager.Identity**](https://msdn.microsoft.com/library/windows/apps/dn705785) будет содержать пустую строку.



```CSharp
using Windows.ApplicationModel.DataTransfer;
using Windows.Security.EnterpriseData;

...

private bool IsClipboardPeekAllowedAsync()
{
    ProtectionPolicyEvaluationResult protectionPolicyEvaluationResult = ProtectionPolicyEvaluationResult.Blocked;
    DataPackageView dataPackageView = Clipboard.GetContent();
    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        protectionPolicyEvaluationResult =
            ProtectionPolicyManager.CheckAccess(dataPackageView.Properties.EnterpriseId,
                ProtectionPolicyManager.GetForCurrentView().Identity);
    }

    // Since this is a convenience feature to allow a peek of clipboard content,
    // if state is Blocked or ConsentRequired, do not show peek if this helper
    // method returns false.
    return (protectionPolicyEvaluationResult == ProtectionPolicyEvaluationResult.Allowed);
}
```

## Запрос доступа для операции копирования и вставки


Этот сценарий показывает, как проверять доступ для операции вставки.

**Примечание**  Этот пример кода зависит от того, установлено ли для текущего представления удостоверение в объекте диспетчера политики защиты (см. раздел [Пометка определенного окна с помощью корпоративного удостоверения](#tag_window_with_id)). В противном случае свойство [**ProtectionPolicyManager.Identity**](https://msdn.microsoft.com/library/windows/apps/dn705785) будет содержать пустую строку.



```CSharp
using Windows.ApplicationModel.DataTransfer;
using Windows.Security.EnterpriseData;

...

private async void OnPasteWithRequestAccess()
{
    DataPackageView dataPackageView = Clipboard.GetContent();
    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        ProtectionPolicyEvaluationResult protectionPolicyEvaluationResult =
            ProtectionPolicyManager.CheckAccess(dataPackageView.Properties.EnterpriseId,
                ProtectionPolicyManager.GetForCurrentView().Identity);

        if (protectionPolicyEvaluationResult == ProtectionPolicyEvaluationResult.Allowed)
        {
            try
            {
                string contentsOfClipboard = await dataPackageView.GetTextAsync();
                // Code goes here to insert the text into the app...
                this.fileContentsTextBox.Text = contentsOfClipboard;
            }
            catch (Exception)
            {
                // Code goes here to handle the exception retrieving text from the clipboard.
            }
        }
        else
        {
            // Paste from the enterprise context is not allowed by policy.
        }
    }
    else
    {
        // The value on the clipboard is not in the text format.
    }
}
```

**Примечание.**  Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).



## Связанные разделы


[Пример защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409)

[**Пространство имен Windows.Security.EnterpriseData**](https://msdn.microsoft.com/library/windows/apps/dn279153)







<!--HONumber=Mar16_HO5-->



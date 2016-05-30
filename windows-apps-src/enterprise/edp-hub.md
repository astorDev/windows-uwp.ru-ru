---
author: mcleblanc
Description: 'Это одна из ключевых тем руководства, позволяющая разработчикам сформировать полное представление о том, как защита корпоративных данных (EDP) связана с файлами, буферами, буфером обмена, сетью, фоновыми задачами и защитой заблокированных данных.'
MS-HAID: 'dev\_enterprise.edp\_hub'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: 'Защита корпоративных данных (EDP)'
---

# Защита корпоративных данных (EDP)

__Примечание.__ Политику защиты корпоративных данных (EDP) нельзя применить в Windows 10 версии 1511 (сборка 10586) или более ранних версий.

Это одна из ключевых тем руководства, позволяющая разработчикам сформировать полное представление о том, как защита корпоративных данных (EDP) связана с файлами, буферами, буфером обмена, сетью, фоновыми задачами и защитой заблокированных данных.

Дополнительные сведения об EDP с точки зрения конечных пользователей и администраторов см. в разделе [Обзор защиты корпоративных данных (EDP)](https://technet.microsoft.com/library/dn985838(v=vs.85).aspx).

## Что такое EDP?

EDP — это набор функций для настольных компьютеров, ноутбуков, планшетов и телефонов для решений управления мобильными устройствами (MDM). EDP предоставляет предприятиям расширенные возможности контроля над обработкой данных (корпоративных файлов и BLOB-объектов) на устройствах, которыми управляет предприятие.

-   Корпоративные данные связаны с шифрованием. Это «защищенные корпоративные данные» или просто защищенные данные».
-   Только приложения, которые явным образом разрешены предприятием в политике EDP, могут получать доступ к защищаемым корпоративным данным, например к файлам.
-   Только приложения, явным образом разрешенные с помощью политики EDP, имеют доступ к корпоративной виртуальной частной сети (VPN).
-   Политика ограничения приложений также определяет, как разрешенные приложения должны обрабатывать корпоративные данные.
-   Ограничения на основе политики применяются даже к корпоративному содержимому, передаваемому через буфер обмена Windows или через контракт отправки данных.
-   По требованию управляющее предприятие может отозвать доступ устройства к защищенному содержимому, по сути удалив корпоративные данные с устройства, но не затрагивая персональных данных.
-   Приложение канала — это приложение, которое загружает защищенные данные. Примерами являются приложения для синхронизации почты и файлов.

EDP расширяет возможности [шифрованной файловой системы EFS](http://technet.microsoft.com/library/cc700811.aspx) и [выборочной очистки Windows](https://technet.microsoft.com/library/dn486874.aspx) для обеспечения большей безопасности и гибкости. Новые API-интерфейсы EDP позволяют создавать приложения, которые защищают доступ к корпоративному содержимому и отзывают его, работают со свойствами защищенных файлов, а также получают доступ к зашифрованным данным в их необработанной форме. Помимо новых интерфейсов API для защиты файлов и папок, также предоставляются интерфейсы API для защиты буферов и потоков. Также предоставляется набор интерфейсов API, которые позволяют приложениям определять и указывать предприятие, которое должно обеспечивать политику защиты данных.

Чтобы предприятие могло управлять доступом к своим защищенным данным, политика ограничений приложений определяет список приложений, а также связанные с ними ограничения. По умолчанию приложение не может получить автономный доступ к защищенным данным. Для получения доступа приложение должно быть добавлено в список, называемый списком разрешенных приложений. Приложения в этом списке называются разрешенными приложениями. На управляемом устройстве Windows может ограничить доступ и выполнить аудит доступа к защищенным данных в буфере обмена, а также аудит доступа через контракт отправки данных, чтобы доступ приложения, не содержащегося в списке разрешенных приложений, проверялся, требовал пользовательского согласия или же полностью блокировался.

Политика для EDP предоставляется устройству управляющим предприятием (устройство становится «управляемым»). Подготовка политики может осуществляться с помощью регистрации пользователя в системе управления мобильными устройствами (MDM), с помощью ручной настройки, выполняемой ИТ-отделом, или через иной механизм управления политиками и их предоставления, например System Center Configuration Manager (SCCM).

Защита файлов с помощью EDP использует ключи службы управления правами (RMS), если они подготовлены, так как эти ключи могут перемещаться между устройствами, тем самым обеспечивая перемещение защищенных данных. При отсутствии ключей RMS эти интерфейсы API возвращаются к использованию ключей выборочной очистки, ограничивая функциональные возможности перемещения. Данные, которые перемещаются в зашифрованном виде, будут доступны на компьютерах с Windows предыдущих версий, а также на сторонних устройствах через приложения RMS от Microsoft для соответствующих платформ, а также через сторонние приложения, поддерживающие RMS.

Подводя итог: предприятия могут управлять данными, защищенными с помощью интерфейсов EDP API. Поэтому можно создавать приложения, которые помогут им обеспечивать защиту данных и управлять этими данными. Другими словами, вы можете создать приложение, поддерживающие корпоративные требования. Оставшаяся часть руководства посвящена именно этой теме.

## Настройка компьютера для EDP


Чтобы надлежащим образом разработать свое приложение и протестировать, как оно будет вести себя в корпоративной среде, необходимо обеспечить на своем компьютере или устройстве надлежащие условия. Сюда входят некоторые задачи, которые, как правило, находятся в компетенции ИТ-администраторов.

-   Ваш компьютер разработчика должен быть зарегистрирован в системе управления мобильными устройствами (MDM).
-   Необходимо добавить приложение в список разрешенных приложений через [поставщика услуг конфигурации AppLocker](https://msdn.microsoft.com/library/windows/hardware/dn920019).

Следующая задача заключается в создании приложения, которое поддерживает управляемые удостоверения на предприятии, может динамически на них реагировать, а также применять политику защиты. Это означает «просвещение» вашего приложения. Приложения, которое следует политике, с большей вероятностью окажется в списке приложений, имеющих доступ к корпоративным данным.

## Корпоративно-грамотные приложения


После включения приложения в список разрешенных оно может считывать защищенные данные. По умолчанию любые выходные данные вашего приложения автоматически защищаются системой. Эта автоматическая защита связана с тем, что управляющее предприятие должно так или иначе обеспечить наличие полного контроля над своими данными. Однако такое ограничение вашего приложение является стандартным способом достижения этой цели. Гораздо лучше обеспечить достаточный уровень доверия системы, который позволит предоставить вам большую свободу действий и гибкость. А для этого необходимо создать более интеллектуальное приложение. Это означает не просто добавить приложение в список разрешенных, а сделать его корпоративно-грамотным и объявить его в качестве такового.

Приложение является корпоративно-грамотным, если оно задействует описываемые нами методы для защиты корпоративных данных во время их хранения, использования и перемещения. Это приложение распознает корпоративные источники данных и сами корпоративные данные и защищает их, как только они попадают в приложение. Эта поддержка также означает соблюдение политики EDP, когда корпоративные данные покидают ваше приложение. Сюда относится запрет на передачу содержимого конечным точкам, находящимся за пределами корпоративной сети, шифрование данных в портативном зашифрованном виде перед перемещением на другие устройства и, возможно (в зависимости от параметров политики), запрос пользователя о необходимости вставки корпоративных данных в приложение, которое отсутствует в списке разрешенных. После обеспечения корпоративной грамотности вашего приложения оно объявит об этом системе, а также сообщит об ограниченной возможности **enterpriseDataPolicy**. Дополнительные сведения о работе с ограниченными возможностями см. в разделе [Специальные и ограниченные возможности](https://msdn.microsoft.com/library/windows/apps/mt270968#special_and_restricted_capabilities).

В идеале все корпоративные данные являются защищенными при их хранении и перемещении. Однако невозможно избежать короткого промежутка времени между созданием корпоративных данных и обеспечением их защиты. Иногда корпоративные данные могут храниться в конечной точке в корпоративной сети в незашифрованном виде. Приложение, поддерживающее корпоративную безопасность, может автономно защищать эти данные, а разрешенным приложениям, которые не являются корпоративно-грамотными, необходима защита, предоставляемая системой.

Это связано с тем, что приложение без поддержки корпоративной безопасности, всегда работает в режиме предприятия. Система обеспечивает соответствующую поддержку. Однако корпоративно-грамотное приложение может переключаться между режимом предприятия и персональном режимом по своему усмотрению, в соответствии с типом обрабатываемых в настоящее время данных. Кроме того, приложение, поддерживающее корпоративную безопасность, должно уважительно относится к персональным данным и не помечать их как корпоративные. Приложение, поддерживающее корпоративную безопасность, может одновременно обрабатывать и корпоративные, и персональные данные, пока выполняются соответствующие заявления. В следующем разделе показано, как переключать режимы в коде.

## Подтверждение управления удостоверением и определение уровня применения политики защиты

В целом ваше приложение получает корпоративное удостоверение из внешнего ресурса, например адреса электронной почты, управляемого домена или имени узла URI. Вы можете вызвать [**ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync**](https://msdn.microsoft.com/library/windows/apps/dn706027) для получения управляемого удостоверения, если таковое имеется, для имени узла конечной точки сети.

Этот пример кода показывает общую структуру корпоративно-грамотного поведения, включая определение фактического управления корпоративным удостоверением и применяемой в настоящее время политики.

```CSharp
using Windows.Security.EnterpriseData;

...

string identity = "contoso.com";

if (ProtectionPolicyManager.IsIdentityManaged(identity))
{
    EnforcementLevel enforcementLevel = ProtectionPolicyManager.GetEnforcementLevel();

    // During UI activities or network access, call ProtectionPolicyManager APIs
    // (taking the enforcement level into account) to ensure that the system
    // tags data with the identity as appropriate.

    ProtectionPolicyManager.GetForCurrentView().Identity = identity;
    // The app is now in enterprise mode.

    ProtectionPolicyManager.GetForCurrentView().Identity = string.Empty;
    // The app is back in personal mode.
}
else
{
    // No policy enforcement is done on this identity.
}
```

Как показано, вы сначала определяете, установлена ли политика EDP для корпоративного удостоверения. Термин «управляемый» означает «управляемый политикой EDP». Если политика EDP установлена для определенного удостоверения, [**ProtectionPolicyManager.IsIdentityManaged**](https://msdn.microsoft.com/library/windows/apps/dn705171) возвращает значение true для этого удостоверения. Это подсказка, что с этим удостоверением надо использовать API-интерфейсы EDP. Хотя API-интерфейсы файлов и буфера являются в своем роде исключениями, поскольку они могут функционировать, как положено, даже в случае неуправляемых удостоверений, такой сценарий не представляется логичным. Если устройство является управляемым, оно управляется в рамках корпоративного удостоверения. Если удостоверение не является управляемым, защита данных для этого удостоверения не имеет смысла.

Следующим шагом является определение и реализация уровня применения политики. Чтобы определить уровень применения политики, вызовите метод [**GetEnforcementLevel**](https://msdn.microsoft.com/library/windows/apps/mt608406). Если корпоративная политика применяется к удостоверению, корпоративно-грамотное приложение должно помогать системе применять политику путем вызова API-интерфейсов [**ProtectionPolicyManager**](https://msdn.microsoft.com/library/windows/apps/dn705170) во время работы пользовательского интерфейса или осуществления сетевого доступа. Это позволит системе в случае необходимости помечать данные с помощью данного удостоверения при их передаче. В этом руководстве приведены дополнительные сведения об интерпретации уровня применения и его реализации на практике. В этом примере кода также показано, как перейти в корпоративный режим или вернуться в персональный режим путем определения значения [**ProtectionPolicyManager.Identity**](https://msdn.microsoft.com/library/windows/apps/dn705785) в качестве корпоративного удостоверения или в качестве пустой строки соответственно. Еще раз обратите внимание, что переход в корпоративный режим и выход из него имеет смысл только в случае управляемого удостоверения.

## Обзор функций EDP


**Защита файлов и буфера.**

-   Ваше приложение может защищать, размещать в контейнере и стирать данные, связанные с корпоративным удостоверением.
-   Управление ключами обрабатывается Windows. Windows использует корпоративные ключи RMS, если они доступны для устройства. В противном случае Windows возвращается к локальной защите с использованием выборочной очистки.

**Управление политикой устройства.**

-   Ваше приложение может запросить удостоверение (корпоративное или организационное), которое управляет устройством.
-   Ваше приложение может защитить пользователей от случайного раскрытия данных путем привязки удостоверения к этим данным.
-   Ваше приложение может защитить корпоративные ресурсы по сети, проверяя подключение к корпоративным сетевым конечным точкам (серверам, диапазонам IP-адресов) и привязывая данные к управляемому удостоверению (т. е. зарегистрированному в MDM).
-   API-интерфейсы EDP работают только с управляемыми удостоверениями, для которых на устройстве определена политика EDP. Если удостоверение не является управляемым, API-интерфейсы при необходимости оповещают об этом приложение.

Ниже приведен список ссылок на темы, в которых подробно рассматриваются API-интерфейсы EDP и сценарии, относящиеся к этим определенным функциональным областям.

## Файлы

См. [Использование EDP для защиты файлов](../files/protect-your-enterprise-data-with-edp.md).

## Потоки и буферы

См. [Использование EDP для защиты потоков и буферов](../files/use-edp-to-protect-streams-and-buffers.md).

## Буфер обмена, общий доступ и обмен данными между приложениями

См. [Использование EDP для защиты корпоративных данных, переносимых между приложениями](../app-to-app/use-edp-to-protect-enterprise-data-transferred-between-apps.md).

## Сеть

См. [Маркировка сетевых подключений с помощью удостоверения EDP](../networking/tagging_network_connections_with_edp_identity.md).

## Защита данных в режиме блокировки (DPL), и фоновые задачи

Организация может выбрать администрирование безопасной политики защиты данных в режиме блокировки (DPL), если ключи шифрования, необходимые для доступа к защищенным ресурсам, временно перемещены из памяти устройства при блокировании этого устройства. Для подготовки к этому приложения изучите раздел [Как обрабатывать события блокировки на устройстве и избегать оставления незащищенного содержимого в памяти](#handle_lock_events) в этой теме. Кроме того, если приложение содержит фоновую задачу, которая должна защищать файлы, см. раздел [Защита корпоративных данных в новом файле (для фоновой задачи)](../files/protect-your-enterprise-data-with-edp.md#protect_data_new_file_bg).

## Применение политики пользовательского интерфейса


В этом разделе мы рассмотрим пример почтового приложения, поддерживающего корпоративную защиту, которое в настоящее время отображает корпоративный почтовый ящик в целом наборе почтовых ящиков, включающих и корпоративные, и личные пользовательские почтовые ящики. Чтобы убедиться в отсутствии утечки корпоративных данных из корпоративного почтового ящика, приложение вызывает [**ProtectionPolicyManager.TryApplyProcessUIPolicy**](https://msdn.microsoft.com/library/windows/apps/dn705791), проверяя, знает ли система о текущем контексте приложения, корпоративном или персональном. Интерфейс API возвращает значение false, если удостоверение управляется корпоративной политикой.

```CSharp
using Windows.Security.EnterpriseData;

...

public class Mailbox
{
    public bool HasEnterpriseMail { get { /* implementation */ } }
    public string Identity { get { /* implementation */ } }
}

private void SwitchMailbox(Mailbox targetMailbox)
{
    // Code goes here to perform setup for "targetMailbox".

    if (targetMailbox.HasEnterpriseMail)
    {
        bool result = 
            ProtectionPolicyManager.TryApplyProcessUIPolicy(targetMailbox.Identity);

        // Code goes here to process "result", which indicates whether
        // or not policy enforcement is in place for the identity.
    }
    else
    {
        // For personal mailboxes, we clear policy enforcement (in case
        // it is still set from when we processed an enterprise mailbox).
        ProtectionPolicyManager.ClearProcessUIPolicy();
    }
}
```

## Как обрабатывать события блокировки устройства и не оставлять незащищенное содержимое в памяти


В этом сценарии мы рассмотрим пример почтового приложения, поддерживающего корпоративную безопасность, которое поддерживает работу с корпоративной и личной почтой. При работе приложения в организации, использующей безопасную политику защиты данных после блокировки (DPL), приложение должно гарантированно удалять все конфиденциальные данные из памяти на время блокировки устройства. Для этого оно регистрируется для получения событий [**ProtectionPolicyManager.ProtectedAccessSuspending**](https://msdn.microsoft.com/library/windows/apps/dn705787) и [**ProtectionPolicyManager.ProtectedAccessResumed**](https://msdn.microsoft.com/library/windows/apps/dn705786), чтобы получать уведомления, когда устройство блокируется и разблокируется (при использовании политики DPL).

[
              **ProtectedAccessSuspending**
            ](https://msdn.microsoft.com/library/windows/apps/dn705787) возникает перед временным удалением подготовленных ключей защиты данных на устройстве. Причиной удаления этих ключей во время блокировки устройства является необходимость блокировки неавторизованного доступа к зашифрованным данным на заблокированном устройстве, которое может находиться не в руках владельца. [
              **ProtectedAccessResumed**
            ](https://msdn.microsoft.com/library/windows/apps/dn705786) создается, как только ключи становятся доступными после разблокировки устройства.

Обработка этих двух событий позволяет приложению гарантировать защиту любого конфиденциального содержимого в памяти с помощью [**DataProtectionManager**](https://msdn.microsoft.com/library/windows/apps/dn706017). Оно также должно закрывать любые файловые потоки, открытые с использованием защищенных файлов, чтобы система не кэшировала в памяти какие-либо конфиденциальные данные. Это можно сделать несколькими способами. Чтобы закрыть файловый поток, возвращенный методом Open для **StorageFile**, можно вызвать метод **Dispose** для этого потока. Можно определить область использования потока с помощью оператора (C\# или VB). Также можно поместить объект **DataReader** или **DataWriter** в качестве оболочки потока и использовать применительно к этому объекту метод **Dispose** или оператор.

**Примечание**  
В среде без настроенной политики DPL возникает событие [**ProtectedAccessResumed**](https://msdn.microsoft.com/library/windows/apps/dn705786), но не событие [**ProtectedAccessSuspending**](https://msdn.microsoft.com/library/windows/apps/dn705787). Об этом следует помнить при составлении кода. Кроме того, не предполагайте, что эти события всегда идут парами в любой системе или что вы можете использовать эти события для определения заблокированного/незаблокированного состояния устройства. В этом примере кода видно, что мы не предполагаем что-либо о защищенном состоянии отображаемого в настоящее время адреса электронной почты или об открытом состоянии потока файла базы данных.

Кроме того, имейте в виду, что при возобновлении работы после блокировки на устройстве без настроенной политики DPL  [**ProtectedAccessResumedEventArgs.Identities**](https://msdn.microsoft.com/library/windows/apps/dn705772) является пустой коллекцией.

Для краткости этот пример кода несколько упрощен. В нем освещена работа с корпоративной почтой. В настоящем приложении личная почта будет записываться в другой, незащищенный файл почтовой базы данных, и вы не будете защищать личные сообщения в режиме блокировки.

```CSharp
using Windows.Security.Cryptography;
using Windows.Security.EnterpriseData;
using Windows.Storage;
using Windows.Storage.Streams;

...

public class DisplayedMail
{
    public string Body { get; set; }
    public IBuffer ProtectedBody { get; set; }
    public bool IsProtected { get; set; }
}

private IOutputStream mailDatabaseStream = null;
private string currentlyDisplayedMailIdentity = "contoso.com";
private DisplayedMail currentlyDisplayedMail = new DisplayedMail()
    { Body = "Lorem ipsum dolor...", ProtectedBody = null, IsProtected = false };

// Gets the app's protected mail database file, then opens and stores a stream on it.
private async void OpenMailDatabase()
{
    // Only attempt to open the database file stream if we know it's closed.
    if (this.mailDatabaseStream == null)
    {
        StorageFolder appDataStorageFolder = ApplicationData.Current.LocalFolder;
        StorageFile storageFile = await appDataStorageFolder.GetFileAsync("maildb.dat");
        using (IRandomAccessStream randomAccessStream =
            await storageFile.OpenAsync(FileAccessMode.ReadWrite))
        {
            this.mailDatabaseStream = randomAccessStream.GetOutputStreamAt(0);
        }
    }
}

// Called once by the app when starting up.
private void AppSetup()
{
    ProtectionPolicyManager.ProtectedAccessSuspending +=
        this.ProtectionPolicyManager_ProtectedAccessSuspending;
    ProtectionPolicyManager.ProtectedAccessResumed +=
        this.ProtectionPolicyManager_ProtectedAccessResumed;
    this.OpenMailDatabase();
}

// Background work called when the app receives an email.
private async void AppMailReceived(string fauxEmail)
{
    // Only attempt to write to the database file stream if we know it's open.
    if (this.mailDatabaseStream != null)
    {
        IBuffer emailAsBuffer = CryptographicBuffer.ConvertStringToBinary
            (fauxEmail, BinaryStringEncoding.Utf8);
        await this.mailDatabaseStream.WriteAsync(emailAsBuffer);
        await this.mailDatabaseStream.FlushAsync();
    }
    else
    {
        // Code goes here to handle the case where the device is
        // locked and we can't access the protected mail database.
    }
}

// Called by ProtectionPolicyManager when the device is locked if under DPL.
private async void ProtectionPolicyManager_ProtectedAccessSuspending
    (object sender, ProtectedAccessSuspendingEventArgs e)
{
    if (!new System.Collections.Generic.List<string>(e.Identities).Contains
        (this.currentlyDisplayedMailIdentity))
    {
        // This event is not for our identity. Another will be sent for our identity.
        return;
    }

    // Get suspension deferral.
    Windows.Foundation.Deferral deferral = e.GetDeferral();

    // Protect the displayed mail content.
    if (!this.currentlyDisplayedMail.IsProtected)
    {
        IBuffer mailBodyBuffer = CryptographicBuffer.ConvertStringToBinary
            (this.currentlyDisplayedMail.Body, BinaryStringEncoding.Utf8);
        BufferProtectUnprotectResult result = await DataProtectionManager.ProtectAsync
            (mailBodyBuffer, this.currentlyDisplayedMailIdentity);
        if (result.ProtectionInfo.Status == DataProtectionStatus.Protected)
        {
            // Save the protected version and clear the unprotected version.
            this.currentlyDisplayedMail.ProtectedBody = result.Buffer;
            this.currentlyDisplayedMail.Body = null;
        }
    }

    // Close the mail database file stream to make sure that we have no unprotected
    // content in memory.
    this.mailDatabaseStream.Dispose();
    this.mailDatabaseStream = null;

    // Optionally, code goes here to use e.Deadline to determine whether we have more
    // than 15 seconds left before the suspension deadline. If we do then process any
    // messages queued up for sending while we are still able to access them.

    // All done. Complete deferral.
    deferral.Complete();
}

// Called by ProtectionPolicyManager when the device is unlocked.
private async void ProtectionPolicyManager_ProtectedAccessResumed
    (object sender, ProtectedAccessResumedEventArgs e)
{
    if (!new System.Collections.Generic.List<string>(e.Identities).Contains
        (this.currentlyDisplayedMailIdentity))
    {
        // This event is not for our identity. Another will be sent for our identity.
        return;
    }

    // Unprotect the displayed mail content.
    if (this.currentlyDisplayedMail.IsProtected)
    {
        BufferProtectUnprotectResult result = await DataProtectionManager.UnprotectAsync
            (this.currentlyDisplayedMail.ProtectedBody);
        if (result.ProtectionInfo.Status == DataProtectionStatus.Unprotected)
        {
            // Restore the unprotected version.
            this.currentlyDisplayedMail.Body = CryptographicBuffer.ConvertBinaryToString
                (BinaryStringEncoding.Utf8, result.Buffer);
            this.currentlyDisplayedMail.ProtectedBody = null;
        }
    }

    // Reopen the closed mail database.
    this.OpenMailDatabase();
}
```

## Регистрация для получения уведомлений при отзыве защищенного содержимого


Представьте сценарий, в котором почтовое приложение настроило корпоративный почтовый ящик на пользовательском устройстве. На некотором этапе и по нескольким возможным причинам предприятие хочет отозвать для этого устройства доступ к защищенной корпоративной почте и другим ресурсам. Существует несколько возможных причин для такого отзыва. Скорее всего, определенная корпоративная политика включит отзыв при отмене регистрации, поэтому один из сценариев будет таким: пользователь отменил регистрацию своего устройства на предприятии (возможно, он подарил ил продал устройство или хочет использовать другое; он меняет место работы или уходит на пенсию). Другой сценарий: уведомление об отмене регистрации отправляется ИТ-администратором удаленно через с помощью средства управления мобильными устройствами (MDM), например если устройство было потеряно.

Независимо от особенностей каждого случая предприятие отправляет запрос на удаление всей почты с пользовательского устройства, так как она там больше не нужна. Клиент системы удаленного управления на устройстве получает запрос от сервера удаленного управления на предприятии и вызывает [**ProtectionPolicyManager.RevokeContent**](https://msdn.microsoft.com/library/windows/apps/dn705790) для отзыва ключей, необходимых для доступа к содержимому, защищенному на этом устройстве корпоративным удостоверением.

Если ваше приложение должно получать уведомления об отзыве, необходимо выполнить регистрацию для получения события [**ProtectionPolicyManager.ProtectedContentRevoked**](https://msdn.microsoft.com/library/windows/apps/dn705788). Когда ваше приложение получает событие, оно может удалить любые метаданные, связанные с корпоративным почтовым ящиком, которые больше не будут востребованы.

```CSharp
using Windows.Security.EnterpriseData;

...

private string mailIdentity = "contoso.com";

void MailAppSetup()
{
    ProtectionPolicyManager.ProtectedContentRevoked += ProtectionPolicyManager_ProtectedContentRevoked;
    // Code goes here to set up mailbox for 'mailIdentity'.
}

private void ProtectionPolicyManager_ProtectedContentRevoked(object sender, ProtectedContentRevokedEventArgs e)
{
    if (!new System.Collections.Generic.List<string>(e.Identities).Contains
        (this.mailIdentity))
    {
        // This event is not for our identity.
        return;
    }

    // Code goes here to delete any metadata associated with 'mailIdentity'.
}
```

## Клиент удаленного управления запускает удаление защищенных корпоративных данных


На своем личном устройстве пользователь имеет несколько корпоративных файлов, которые защищены корпоративным удостоверением. Пользователь теряет устройство. Когда он уведомляет предприятие о потере устройства, предприятие посылает запрос на удаление всех конфиденциальных данных на пользовательском устройстве во избежание утечки данных. Клиент удаленного управления на устройстве получает этот запрос от сервера удаленного управления на предприятии и вызывает [**ProtectionPolicyManager.RevokeContent**](https://msdn.microsoft.com/library/windows/apps/dn705790) для отзыва ключей, необходимых для доступа к содержимому, защищенному на этом устройстве корпоративным удостоверением.

```CSharp
Windows.Security.EnterpriseData.ProtectionPolicyManager.RevokeContent("contoso.com");
```

 

 





<!--HONumber=May16_HO2-->



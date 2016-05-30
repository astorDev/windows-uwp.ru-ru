---
author: DelfCo
Description: 'В этом разделе показано создание защищенного контекста беседы перед созданием сетевых подключений в сценарии с защитой корпоративных данных (EDP).'
MS-HAID: 'dev\_networking.tagging\_network\_connections\_with\_edp\_identity'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: Пометка сетевых подключений идентификатором EDP
---

# Пометка сетевых подключений идентификатором EDP

__Примечание.__ Политику защиты корпоративных данных (EDP) нельзя применить в Windows 10 версии 1511 (сборка 10586) или более ранних версий.

В этом разделе показано создание защищенного контекста беседы перед созданием сетевых подключений в сценарии с защитой корпоративных данных (EDP). Полное описание взаимосвязи EDP с файлами, потоками, буфером обмена, сетями, фоновыми задачами и защитой данных в режиме блокировки для разработчиков см. в разделе [Защита корпоративных данных (EDP)](../enterprise/edp-hub.md).

**Примечание.**  [Пример защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409) охватывает большинство сценариев EDP.



## Необходимые условия


-   **Настройка для EDP**

    См. раздел [Настройка компьютера для EDP](../enterprise/edp-hub.md#set-up-your-computer-for-EDP).

-   **Создание корпоративно-грамотного приложения**

    Приложение, которое автономно гарантирует, что корпоративные данные останутся под управлением предприятия, называют корпоративно-грамотным приложением. Грамотное приложение более мощное, функциональное, гибкое и надежное, чем неграмотное. Ваше приложение сообщает системе, что оно грамотное, объявляя ограниченную возможность **enterpriseDataPolicy**. Но чтобы приложение было грамотным, недостаточно просто объявить соответствующую возможность. Подробнее: [Корпоративно-грамотные приложения](../enterprise/edp-hub.md#enterprise-enlightened-apps).

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C\# или Visual Basic см. в статье [Вызов асинхронных API в C\# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

## Получение доступа к корпоративным ресурсам через сеть


В этом сценарии почтовое приложение, имеющее необходимые сведения, синхронизирует набор почтовых ящиков, в который входят как корпоративные, так и личные почтовые ящики. Приложение передает удостоверение пользователя вызову [**ProtectionPolicyManager.CreateCurrentThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/dn706025) для создания защищенного контекста потока. При этом маркируются все сетевые подключения, впоследствии созданные в одном и том же потоке с этим же удостоверением. Это позволяет подключиться к корпоративным сетевым ресурсам, доступ к которым контролируется корпоративной политикой.

Термин «корпоративный» относится к предприятию, которому принадлежит удостоверение пользователя. [
              **CreateCurrentThreadNetworkContext**
            ](https://msdn.microsoft.com/library/windows/apps/dn706025) возвращает объект [**ThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/dn706029) независимо от применения политики. Как правило, если приложение должно обработать смешанные ресурсы, оно может выбрать вызов **CreateCurrentThreadNetworkContext** для всех удостоверений. После получения сетевых ресурсов приложение вызывает **Dispose** в **ThreadNetworkContext**, чтобы удалить все теги удостоверения из текущего потока. Шаблон, используемый для ликвидации объекта контекста, будет зависеть от языка программирования.

Если удостоверение неизвестно, приложение может запросить удостоверение, управляемое политикой предприятия, из сетевого адреса ресурса с помощью API [**ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync**](https://msdn.microsoft.com/library/windows/apps/dn706027).

**Примечание.**  Как видно в примере кода, правильный шаблон использования для элемента [**CreateCurrentThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/dn706025) предполагает сведение к минимуму области, в которой он действует. Необходимо установить контекст корпоративной сети, создать сетевые подключения, вернуть контекст, использовать эти подключения, а затем закрыть их. Пример кода ниже иллюстрирует подробные сведения. Важно не создавать файлы в потоке, если контекст корпоративной сети задан в этом потоке. Это может привести к автоматическому шифрованию файла, независимо от того, хотите ли вы, чтобы файл был личным. Это одна из причин, по которым рекомендуется отменить изменения контекста как можно раньше.



```CSharp
using Windows.Data.Xml.Dom;
using Windows.Networking;
using Windows.Security.EnterpriseData;
using Windows.Storage.Streams;
using Windows.Web.Http;

...

public static async void SyncMailbox(string identity)
{
    HttpClient client = null;
    // Create a protected network context for "identity" on the current
    // thread. Network connections made after this call will be tagged
    // to "identity", and will have policy enforced. This is required
    // to access enterprise network resources for "identity".
    using (ThreadNetworkContext threadNetworkContext = 
        ProtectionPolicyManager.CreateCurrentThreadNetworkContext(identity))
    {
        // Retrieve new mail.
        client = new HttpClient();
    }

    string xmlResponse = await client.GetStringAsync
        (new Uri("https://contosomail/getnewmail?user=" + identity));

    // Now, process mail data received.
    var xmlDocument = new XmlDocument();
    xmlDocument.LoadXml(xmlResponse);
    foreach (IXmlNode mailNode in xmlDocument.GetElementsByTagName("mail"))
    {
        // Code to process text data in mail (body, recipients etc.)
        // would go here ...

        // Processes resource links in mail body.
        foreach (IXmlNode childNode in mailNode.ChildNodes)
        {
            if (childNode.NodeName == "resource")
            {
                var resourceUri = new Uri(childNode.InnerText);

                // Check if the resource is on an enterprise-managed endpoint.
                string resourceIdentity =
                    await ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync
                        (new HostName(resourceUri.Host));
                if (!string.IsNullOrEmpty(resourceIdentity))
                {
                    using (ThreadNetworkContext threadNetworkContext =
                        ProtectionPolicyManager.CreateCurrentThreadNetworkContext(resourceIdentity))
                    {
                        client = new HttpClient();
                    }
                    IBuffer data = await client.GetBufferAsync(resourceUri);
                    // Code to process retrieved resource data would go here...
                }
            }
        }
    }
}
```

**Примечание.**  Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).



## Связанные разделы


[Пример защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409)

[**Пространство имен Windows.Security.EnterpriseData**](https://msdn.microsoft.com/library/windows/apps/dn279153)

 

 





<!--HONumber=May16_HO2-->



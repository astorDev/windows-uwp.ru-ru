---
author: TylerMSFT
ms.assetid: 066711E0-D5C4-467E-8683-3CC64EDBCC83
title: "Вызов асинхронных API в C# и Visual Basic"
description: "Универсальная платформа Windows (UWP) включает много асинхронных API, позволяющих вашему приложению сохранить способность отвечать, когда выполняется работа, требующая много времени."
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, C#, Visual Basic, асинхронный"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 0bafeaf66ec95732d2444debcfdca7b9406ddc4e
ms.lasthandoff: 02/07/2017

---
# <a name="call-asynchronous-apis-in-c-or-visual-basic"></a>Вызов асинхронных API в C# и Visual Basic

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Универсальная платформа Windows (UWP) включает много асинхронных API, позволяющих вашему приложению сохранить способность отвечать, когда выполняется работа, требующая много времени. В этом разделе описывается применение асинхронных методов UWP при программировании на языках C# или Microsoft Visual Basic.

При применении асинхронных API-интерфейсов приложению не требуется ждать завершения трудоемких операций. Например, приложение, скачивающее информацию из Интернета, может ожидать получения данных несколько секунд. Если информация получается синхронным методом, приложение блокируется до момента возврата данных методом. Приложение при этом не отвечает на действия пользователя и кажется зависшим, что может встревожить пользователя. Благодаря асинхронным API, предоставляемым UWP, приложение сохраняет способность отвечать на запросы пользователя при выполнении длительных операций.

У большинства асинхронных API в UWP нет синхронных аналогов, поэтому необходимо понимать, как следует использовать асинхронные API в приложении UWP на языках C# или Visual Basic. Здесь мы покажем, как вызывать асинхронные API UWP.

## <a name="using-asynchronous-apis"></a>Использование асинхронных API


По соглашению асинхронным методам даются имена, которые заканчиваются на «Async». Асинхронные API обычно вызываются в ответ на действие пользователя, например при нажатии кнопки. Вызов асинхронного метода в обработчике событий — один из простейших способов использования асинхронных API. Здесь в качестве примера используется оператор **await**.

Предположим, некоторое приложение перечисляет заголовки записей в блоге из определенного веб-сайта. В приложении имеется [**Button**](https://msdn.microsoft.com/library/windows/apps/BR209265) (кнопка), которую пользователи нажимают для получения заголовков. Заголовки отображаются в [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652). Когда пользователи нажимают данную кнопку, важно, чтобы приложение сохраняло способность отвечать, пока ожидается информация от веб-сайта блога. Для обеспечения этой способности UWP предоставляет асинхронный метод [**SyndicationClient.RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460) для скачивания веб-канала.

Здесь дан пример кода для получения из блога списка его записей с помощью вызова асинхронного метода [**SyndicationClient.RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460) и ожидания получения результата.

> [!div class="tabbedCodeSnippets" data-resources="OutlookServices.Calendar"]
[!code-csharp[Main](./AsyncSnippets/csharp/MainPage.xaml.cs#SnippetDownloadRSS)]
[!code-vb[Main](./AsyncSnippets/vbnet/MainPage.xaml.vb#SnippetDownloadRSS)]

Есть несколько важных замечаний о приведенном выше примере. Во-первых, в строке `SyndicationFeed feed = await client.RetrieveFeedAsync(feedUri)` используется оператор **await** вместе с вызовом асинхронного метода [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460). Можно представить себе, что оператор **await** сообщает компилятору о том, что вы вызываете асинхронный метод, в результате чего компилятор выполняет дополнительную работу за вас. Во-вторых, объявление обработчика событий включает ключевое слово **async**. Это ключевое слово необходимо включить в объявление любого метода, в котором используется оператор **await**.

В этом разделе мы не будем углубляться в детали того, что компилятор делает с оператором **await**. Однако давайте изучим, что делает приложение асинхронным и способным отвечать на запросы. Рассмотрим, что происходит при использовании синхронного кода. Например, предположим, что есть синхронный метод `SyndicationClient.RetrieveFeed`. (Такого метода нет, но представим, что он существует.) Если ваше приложение включает строку `SyndicationFeed feed = client.RetrieveFeed(feedUri)` вместо `SyndicationFeed feed = await client.RetrieveFeedAsync(feedUri)`, выполнение приложения останавливается, пока не будет доступно возвращаемое значение `RetrieveFeed`. А пока приложение ожидает завершения выполнения метода, оно не может реагировать на другие события, например на еще одно событие [**Click**](https://msdn.microsoft.com/library/windows/apps/BR227737). То есть приложение будет блокировано, пока метод `RetrieveFeed` не вернет значение.

Однако если вызывать `client.RetrieveFeedAsync`, данный метод инициирует извлечение и немедленно возвращает значение. При использовании оператора **await** с методом [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460) приложение временно завершает работу обработчика событий. Затем оно может обрабатывать любые другие события, пока **RetrieveFeedAsync** выполняется асинхронно. Это позволяет приложению сохранить способность реагировать на действия пользователя. Когда метод **RetrieveFeedAsync** завершает работу и становится доступен [**SyndicationFeed**](https://msdn.microsoft.com/library/windows/apps/BR243485), приложение повторно запускает обработчик событий с момента его закрытия после `SyndicationFeed feed = await client.RetrieveFeedAsync(feedUri)` и завершает оставшуюся часть метода.

Интересная особенность использования оператора **await** заключается в том, что код с его участием не очень отличается от кода с применением воображаемого метода `RetrieveFeed`. Асинхронный код в C# или Visual Basic можно записать тремя способами без оператора **await**, однако полученный код больше подчеркивает механику асинхронного выполнения. Поэтому асинхронный код сложен в написании, понимании и обслуживании. Оператор **await** позволяет получить преимущества асинхронного приложения без создания сложного кода.

## <a name="return-types-and-results-of-asynchronous-apis"></a>Типы возврата и результаты асинхронных API


Если перейти по ссылке [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460), можно заметить, что тип возврата **RetrieveFeedAsync** не [**SyndicationFeed**](https://msdn.microsoft.com/library/windows/apps/BR243485), а `IAsyncOperationWithProgress<SyndicationFeed, RetrievalProgress>`. С точки зрения простого синтаксиса асинхронный API возвращает объект, содержащий результат. Несмотря на то, что принято представлять асинхронный метод как метод, поддерживающий ожидание, и это иногда удобно, оператор **await** фактически работает с возвращаемым значением метода, а не с самим методом. При применении оператора **await** вы получаете результат вызова **GetResult** в объекте, возвращенном этим методом. В примере **SyndicationFeed** является результатом **RetrieveFeedAsync.GetResult()**.

При использовании асинхронного метода можно проверить сигнатуру, чтобы увидеть, что вы получите по окончании ожидания возврата значения метода. Все асинхронные API UWP возвращают один из следующих типов:

-   [**IAsyncOperation&lt;TResult&gt;**](https://msdn.microsoft.com/library/windows/apps/BR206598)
-   [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](https://msdn.microsoft.com/library/windows/apps/BR206594)
-   [**IAsyncAction**](https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncaction.aspx)
-   [**IAsyncActionWithProgress&lt;TProgress&gt;**](https://msdn.microsoft.com/library/windows/apps/br206581.aspx)

Тип результата асинхронного метода тот же, что и у параметра типа `      TResult`. У типов без `TResult` нет результата. Данный результат можно представлять как **void**. В Visual Basic процедура [Sub](https://msdn.microsoft.com/library/windows/apps/xaml/831f9wka.aspx) эквивалентна методу с типом возврата **void**.

В таблице ниже даются примеры асинхронных методов и перечисляются тип возврата и тип результата для каждого из них.

| Асинхронный метод                                                                           | Тип возврата                                                                                                                                        | Тип результата                                       |
|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| [**SyndicationClient.RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/BR243460)     | [**IAsyncOperationWithProgress&lt;SyndicationFeed, RetrievalProgress&gt;**](https://msdn.microsoft.com/library/windows/apps/BR206594)                                 | [**SyndicationFeed**](https://msdn.microsoft.com/library/windows/apps/BR243485) |
| [**FileOpenPicker.PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/JJ635275) | [**IAsyncOperation&lt;StorageFile&gt;**](https://msdn.microsoft.com/library/windows/apps/BR206598)                                                                                | [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171)          |
| [**XmlDocument.SaveToFileAsync**](https://msdn.microsoft.com/library/windows/apps/BR206284)                 | [**IAsyncAction**](https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncaction.aspx)                                                                                                           | **void**                                          |
| [**InkStrokeContainer.LoadAsync**](https://msdn.microsoft.com/library/windows/apps/Hh701757)               | [**IAsyncActionWithProgress&lt;UInt64&gt;**](https://msdn.microsoft.com/library/windows/apps/br206581.aspx)                                                                   | **void**                                          |
| [**DataReader.LoadAsync**](https://msdn.microsoft.com/library/windows/apps/BR208135)                            | [**DataReaderLoadOperation**](https://msdn.microsoft.com/library/windows/apps/BR208120), пользовательский класс результатов, который реализует **IAsyncOperation&lt;UInt32&gt;** | [**UInt32**](https://msdn.microsoft.com/library/windows/apps/br206598.aspx)                     |

 

Асинхронные методы, определенные в [**.NET for UWP apps**](https://msdn.microsoft.com/library/windows/apps/xaml/br230232.aspx), имеют тип возврата [**Task**](https://msdn.microsoft.com/library/windows/apps/xaml/system.threading.tasks.task.aspx) или [**Task&lt;TResult&gt;**](https://msdn.microsoft.com/library/windows/apps/xaml/dd321424.aspx). Методы, возвращающие **Task**, аналогичны асинхронным методам UWP, которые возвращают [**IAsyncAction**](https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncaction.aspx). В каждом случае результат асинхронного метода — **void**. Тип возврата **Task&lt;TResult&gt;** подобен [**IAsyncOperation&lt;TResult&gt;**](https://msdn.microsoft.com/library/windows/apps/BR206598), так как у результата асинхронного метода при выполнении задачи тот же тип, что и у параметра типа `TResult`. Дополнительную информацию об использовании **.NET for UWP apps** и задач см. в статье [Общая информация о .NET для приложений среды выполнения Windows](https://msdn.microsoft.com/library/windows/apps/xaml/br230302.aspx).

## <a name="handling-errors"></a>Обработка ошибок


При использовании оператора **await** для получения результатов из асинхронного метода блок **try/catch** позволяет обрабатывать ошибки, возникающие в асинхронных методах, так же, как и для синхронных методов. В примере выше метод **RetrieveFeedAsync** и оператор **await** заключены в блок **try/catch** для обработки ошибок при вызове исключений.

Когда асинхронные методы вызывают другие асинхронные методы, любой асинхронный метод, приводящий к вызову исключения, распространяется на внешние методы. Это означает, что в самый внешний метод можно поместить блок **try/catch**, чтобы перехватывать ошибки для вложенных асинхронных методов. Это аналогично перехвату исключений для синхронных методов. Однако вы не можете использовать **await** в блоке **catch**.

**Совет.**  Возможность использования оператора **await** в блоке **catch** в С# доступна начиная с Microsoft Visual Studio 2005 и выше.

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

Здесь показан простейший способ вызова асинхронного метода, когда асинхронные API вызываются в обработчике событий. Этот способ также применим, когда асинхронный метод вызывается в переопределенном методе, который возвращает **void** или **Sub** в Visual Basic.

Сталкиваясь с асинхронными методами в UWP, важно помнить следующее.

-   По соглашению асинхронным методам даются имена, которые заканчиваются на «Async».
-   Объявление любого метода, в котором используется оператор **await**, должно быть помечено ключевым словом **async**.
-   Когда приложение находит оператор **await**, оно сохраняет способность взаимодействовать с пользователем, пока выполняется асинхронный метод.
-   В результате ожидания значения асинхронный метод возвращает объект, содержащий результат. В большинстве случаев результат, содержащийся в возвращаемом значении — это то, что используется, а не само возвращаемое значение. Тип значения, содержащегося в результате, можно найти, взглянув на тип возврата асинхронного метода.
-   Благодаря использованию асинхронных API и шаблонов **async** часто можно улучшить скорость отклика приложения.

При выполнении примера из данного раздела выводится текст следующего вида.

``` syntax
Windows Experience Blog
PC Snapshot: Sony VAIO Y, 8/9/2011 10:26:56 AM -07:00
Tech Tuesday Live Twitter #Chat: Too Much Tech #win7tech, 8/8/2011 12:48:26 PM -07:00
Windows 7 themes: what’s new and what’s popular!, 8/4/2011 11:56:28 AM -07:00
PC Snapshot: Toshiba Satellite A665 3D, 8/2/2011 8:59:15 AM -07:00
Time for new school supplies? Find back-to-school deals on Windows 7 PCs and Office 2010, 8/1/2011 2:14:40 PM -07:00
Best PCs for blogging (or working) on the go, 8/1/2011 10:08:14 AM -07:00
Tech Tuesday – Blogging Tips and Tricks–#win7tech, 8/1/2011 9:35:54 AM -07:00
PC Snapshot: Lenovo IdeaPad U460, 7/29/2011 9:23:05 AM -07:00
GIVEAWAY: Survive BlogHer with a Sony VAIO SA and a Samsung Focus, 7/28/2011 7:27:14 AM -07:00
3 Ways to Stay Cool This Summer, 7/26/2011 4:58:23 PM -07:00
Getting RAW support in Photo Gallery & Windows 7 (…and a contest!), 7/26/2011 10:40:51 AM -07:00
Tech Tuesdays Live Twitter Chats: Photography Tips, Tricks and Essentials, 7/25/2011 12:33:06 PM -07:00
3 Tips to Go Green With Your PC, 7/22/2011 9:19:43 AM -07:00
How to: Buy a Green PC, 7/22/2011 9:13:22 AM -07:00
Windows 7 themes: the distinctive artwork of Cheng Ling, 7/20/2011 9:53:07 AM -07:00
```


---
author: stevewhims
description: Получайте или создавайте самое свежее и популярное веб-содержимое с помощью сводных веб-каналов, созданных по стандартам RSS и Atom с помощью компонентов в пространстве имен Windows.Web.Syndication.
title: Каналы RSS и Atom
ms.assetid: B196E19B-4610-4EFA-8FDF-AF9B10D78843
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 309dd2aedb2195362652da93c13648d07e5ea9f8
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5969044"
---
# <a name="rssatom-feeds"></a>Каналы RSS и Atom


**Важные API**

-   [**Windows.Data.Xml.Dom**](https://msdn.microsoft.com/library/windows/apps/br240819)
-   [**Windows.Web.AtomPub**](https://msdn.microsoft.com/library/windows/apps/br210609)
-   [**Windows.Web.Syndication**](https://msdn.microsoft.com/library/windows/apps/br243632)

Получайте или создавайте самое свежее и популярное веб-содержимое с помощью сводных веб-каналов, созданных по стандартам RSS и Atom с помощью компонентов в пространстве имен [**Windows.Web.Syndication**](https://msdn.microsoft.com/library/windows/apps/br243632).

## <a name="what-is-a-feed"></a>Что такое веб-канал?

Веб-канал – это документ, содержащий любое количество отдельных записей, состоящих из текста, ссылок и изображений. Обновления вносятся в веб-канал в виде новых записей, используемых для показа самого свежего содержимого в Интернете. Потребители содержимого могут использовать приложение для чтения веб-каналов, чтобы собирать и отслеживать каналы любого количества отдельных авторов содержимого, быстро и удобно получая доступ к самой свежей информации.

## <a name="which-feed-format-standards-are-supported"></a>Какие версии форматов веб-каналов поддерживаются?

Универсальная платформа Windows (UWP) поддерживает извлечение веб-каналов формата RSS версий 0.91–2.0 и формата Atom версий 0.3–1.0. Классы в пространстве имен [**Windows.Web.Syndication**](https://msdn.microsoft.com/library/windows/apps/br243632) могут определять веб-каналы и их элементы, способные представлять элементы RSS и Atom.

Кроме того, в форматах Atom1.0 и RSS2.0 документы веб-каналов могут содержать элементы или атрибуты, не определенные в официальных спецификациях. Со временем эти особые элементы стали способом определения информации домена, потребляемой другими форматами данных веб-служб, например GData и OData. Чтобы обеспечить поддержку нового компонента, класс [**SyndicationNode**](https://msdn.microsoft.com/library/windows/apps/br243585) представляет универсальные XML-элементы. Использование **SyndicationNode** с классами в пространстве имен [**Windows.Data.Xml.Dom**](https://msdn.microsoft.com/library/windows/apps/br240819) позволяет приложениям получать доступ к атрибутам, расширениям и любому содержимому, которое они могут включать.

Обратите внимание, что для публикации сводного содержимого реализация протокола Atom Publication ([**Windows.Web.AtomPub**](https://msdn.microsoft.com/library/windows/apps/br210609)) платформы UWP поддерживает только операции с содержимым веб-каналов согласно стандартам Atom и Atom Publication.

## <a name="using-syndicated-content-with-network-isolation"></a>Использование сводного содержимого вместе с сетевой изоляцией

Функция сетевой изоляции в UWP позволяет разработчику управлять доступом приложения UWP к сети и ограничивать его. Доступ к сети может требоваться не всем приложениям. Но для приложений, которым он необходим, платформа UWP предоставляет разные уровни доступа к сети, которые обеспечиваются выбором подходящих возможностей.

Сетевая изоляция позволяет разработчику определить для каждого приложения область необходимого доступа к сети. Приложению без соответствующей определенной области запрещен доступ к отдельному типу сети и к отдельному типу сетевых запросов (только исходящим запросам, инициированным клиентом, или им же в сочетании с входящими нежелательными запросами). Возможность настраивать и применять сетевую изоляцию гарантирует, что даже в случае компрометации приложения оно сможет получить доступ только к тем сетям, к которым доступ ему предоставлен явно. Таким образом значительно сокращается область воздействия на другие приложения и Windows.

Сетевая изоляция влияет на все элементы класса в пространствах имен [**Windows.Web.Syndication**](https://msdn.microsoft.com/library/windows/apps/br243632) и [**Windows.Web.AtomPub**](https://msdn.microsoft.com/library/windows/apps/br210609), которые пытаются получить доступ к сети. Сетевая изоляция активно используется в Windows. Вызов элемента класса в пространстве имен **Windows.Web.Syndication** или **Windows.Web.AtomPub**, обеспечивающем доступ к сети, может быть не выполнен из-за сетевой изоляции, если соответствующая возможность работы с сетью не была включена.

Возможности сети для приложения настраиваются в его манифесте при сборке. Они обычно добавляются с помощью Microsoft Visual Studio2015 при разработке приложения. Возможности сети можно также задать вручную в файле манифеста приложения, используя текстовый редактор.

Дополнительные сведения о сетевой изоляции и сетевых возможностях см. в разделе "Возможности" темы [Основы работы с сетями](networking-basics.md).

## <a name="how-to-access-a-web-feed"></a>Доступ к веб-каналу

В этом разделе объясняется, как извлечь и отобразить веб-канал с помощью классов в пространстве имен [**Windows.Web.Syndication**](https://msdn.microsoft.com/library/windows/apps/br243632) в приложении UWP, написанном на языке C# или Javascript.

**Необходимые условия**

Чтобы подготовить приложение UWP для работы в сети, необходимо настроить соответствующие возможности сетевого подключения в файле проекта **Package.appxmanifest**. Если приложению необходимо подключаться в качестве клиента к удаленным службам в Интернете, для этого требуется возможность **internetClient** . Дополнительные сведения см. в разделе "Возможности" темы [Основы работы с сетями](networking-basics.md).

**Получение сводного содержимого из веб-канала**

Теперь рассмотрим код, в котором демонстрируется способ извлечения канала и отображения каждого из его отдельных элементов. Прежде чем настроить и отправить запрос, определим несколько переменных для этой операции, а затем инициализируем экземпляр [**SyndicationClient**](https://msdn.microsoft.com/library/windows/apps/br243456), определяющий методы и свойства, предназначенные для извлечения и отображения канала.

Конструктор [**Uri**](https://msdn.microsoft.com/library/windows/apps/br226017) создает исключение, если параметр *uriString*, переданный конструктору, не является допустимым URI. Поэтому проверяем *uriString* при помощи блока Try/Catch.

> [!div class="tabbedCodeSnippets"]
```csharp
Windows.Web.Syndication.SyndicationClient client = new Windows.Web.Syndication.SyndicationClient();
Windows.Web.Syndication.SyndicationFeed feed;
// The URI is validated by catching exceptions thrown by the Uri constructor.
Uri uri = null;
// Use your own uriString for the feed you are connecting to.
string uriString = "";
try
{
    uri = new Uri(uriString);
}
catch (Exception ex)
{
    // Handle the invalid URI here.
}
```
```javascript
var currentFeed = null;
var currentItemIndex = 0;
var client = new Windows.Web.Syndication.SyndicationClient();
// The URI is validated by catching exceptions thrown by the Uri constructor.
var uri = null;
try {
    uri = new Windows.Foundation.Uri(uriString);
} catch (error) {
    WinJS.log && WinJS.log("Error: Invalid URI");
    return;
}
```

Теперь настроим запрос путем установки всех необходимых учетных данных сервера (свойство [**ServerCredential**](https://msdn.microsoft.com/library/windows/apps/br243461)), учетных данных прокси-сервера (свойство [**ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/br243459)) и заголовков HTTP (метод [**SetRequestHeader**](https://msdn.microsoft.com/library/windows/apps/br243462)). После настройки основных параметров запроса получаем допустимый объект [**Uri**](https://msdn.microsoft.com/library/windows/apps/br226017), созданный с помощью предоставляемой приложением строки URI канала. Затем объект **Uri** передается в функцию [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/br243460) для запроса канала.

Допустим, было возвращено нужное содержимое канала. В этом случае код из примера перебирает каждый элемент канала, вызывая **displayCurrentItem** (который мы определим позже), чтобы отобразить элементы и их содержимое в виде списка с помощью пользовательского интерфейса.

При вызове большинства асинхронных сетевых методов вам нужно создать код для обработки исключений. Обработчик исключений может получить подробную информацию о причине исключения, чтобы разобраться в проблеме и принять необходимые меры.

Метод [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/br243460) создает исключение, если не удается установить подключение к HTTP-серверу или объект [**Uri**](https://msdn.microsoft.com/library/windows/apps/br226017) не указывает на действительный AtomPub или RSS-канал. В примере кода Javascript используется функция **onError** для захвата любых исключений и вывода подробной информации об исключении при ошибке.

> [!div class="tabbedCodeSnippets"]
```csharp
try
{
    // Although most HTTP servers do not require User-Agent header, 
    // others will reject the request or return a different response if this header is missing.
    // Use the setRequestHeader() method to add custom headers.
    client.SetRequestHeader("User-Agent", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)");
    feed = await client.RetrieveFeedAsync(uri);
    // Retrieve the title of the feed and store it in a string.
    string title = feed.Title.Text;
    // Iterate through each feed item.
    foreach (Windows.Web.Syndication.SyndicationItem item in feed.Items)
    {
        displayCurrentItem(item);
    }
}
catch (Exception ex)
{
    // Handle the exception here.
}
```
```javascript
function onError(err) {
    WinJS.log && WinJS.log(err, "sample", "error");
    // Match error number with a ErrorStatus value.
    // Use Windows.Web.WebErrorStatus.getStatus() to retrieve HTTP error status codes.
    var errorStatus = Windows.Web.Syndication.SyndicationError.getStatus(err.number);
    if (errorStatus === Windows.Web.Syndication.SyndicationErrorStatus.invalidXml) {
        displayLog("An invalid XML exception was thrown. Please make sure to use a URI that points to a RSS or Atom feed.");
    }
}
// Retrieve and display feed at given feed address.
function retreiveFeed(uri) {
    // Although most HTTP servers do not require User-Agent header, 
    // others will reject the request or return a different response if this header is missing.
    // Use the setRequestHeader() method to add custom headers.
    client.setRequestHeader("User-Agent", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)");
    client.retrieveFeedAsync(uri).done(function (feed) {
        currentFeed = feed;
        WinJS.log && WinJS.log("Feed download complete.", "sample", "status");
        var title = "(no title)";
        if (currentFeed.title) {
            title = currentFeed.title.text;
        }
        document.getElementById("CurrentFeedTitle").innerText = title;
        currentItemIndex = 0;
        if (currentFeed.items.size > 0) {
            displayCurrentItem();
        }
        // List the items.
        displayLog("Items: " + currentFeed.items.size);
     }, onError);
}
```

В предыдущем шаге метод [**RetrieveFeedAsync**](https://msdn.microsoft.com/library/windows/apps/br243460) вернул запрошенное содержимое канала, а код примера перебрал все доступные элементы канала. Каждый из этих элементов представляется с помощью объекта [**SyndicationItem**](https://msdn.microsoft.com/library/windows/apps/br243533), содержащего все свойства элемента и содержимое, которое предоставляется соответствующим стандартом синдикации (RSS или Atom). В следующем примере рассматривается функция **displayCurrentItem**, которая обрабатывает каждый элемент и отображает его содержимое с помощью элементов пользовательского интерфейса с различными именами.

> [!div class="tabbedCodeSnippets"]
```csharp
private void displayCurrentItem(Windows.Web.Syndication.SyndicationItem item)
{
    string itemTitle = item.Title == null ? "No title" : item.Title.Text;
    string itemLink = item.Links == null ? "No link" : item.Links.FirstOrDefault().ToString();
    string itemContent = item.Content == null ? "No content" : item.Content.Text;
    //displayCurrentItem is continued below.
```
```javascript
function displayCurrentItem() {
    var item = currentFeed.items[currentItemIndex];
    // Display item number.
    document.getElementById("Index").innerText = (currentItemIndex + 1) + " of " + currentFeed.items.size;
    // Display title.
    var title = "(no title)";
    if (item.title) {
        title = item.title.text;
    }
    document.getElementById("ItemTitle").innerText = title;
    // Display the main link.
    var link = "";
    if (item.links.size > 0) {
        link = item.links[0].uri.absoluteUri;
    }
    var link = document.getElementById("Link");
    link.innerText = link;
    link.href = link;
    // Display the body as HTML.
    var content = "(no content)";
    if (item.content) {
        content = item.content.text;
    }
    else if (item.summary) {
        content = item.summary.text;
    }
    document.getElementById("WebView").innerHTML = window.toStaticHTML(content);
                //displayCurrentItem is continued below.
```

Как упоминалось выше, содержимое, представленное объектом [**SyndicationItem**](https://msdn.microsoft.com/library/windows/apps/br243533), будет различаться в зависимости от того, какой стандарт (RSS или Atom) используется для публикации веб-канала. Например, веб-канал Atom может предоставлять список [**Contributors**](https://msdn.microsoft.com/library/windows/apps/br243540), а RSS-канал — нет. Но входящие в элемент канала элементы расширения, которые не поддерживаются никаким стандартом (например, элементы расширения Dublin Core), доступны с помощью свойства [**SyndicationItem.ElementExtensions**](https://msdn.microsoft.com/library/windows/apps/br243543) и отображаются, как показано в следующем примере кода.

> [!div class="tabbedCodeSnippets"]
```csharp
    //displayCurrentItem continued.
    string extensions = "";
    foreach (Windows.Web.Syndication.SyndicationNode node in item.ElementExtensions)
    {
        string nodeName = node.NodeName;
        string nodeNamespace = node.NodeNamespace;
        string nodeValue = node.NodeValue;
        extensions += nodeName + "\n" + nodeNamespace + "\n" + nodeValue + "\n";
    }
    this.listView.Items.Add(itemTitle + "\n" + itemLink + "\n" + itemContent + "\n" + extensions);
}
```
```javascript
    // displayCurrentItem function continued.
    var bindableNodes = [];
    for (var i = 0; i < item.elementExtensions.size; i++) {
        var bindableNode = {
            nodeName: item.elementExtensions[i].nodeName,
             nodeNamespace: item.elementExtensions[i].nodeNamespace,
             nodeValue: item.elementExtensions[i].nodeValue,
        };
        bindableNodes.push(bindableNode);
    }
    var dataList = new WinJS.Binding.List(bindableNodes);
    var listView = document.getElementById("extensionsListView").winControl;
    WinJS.UI.setOptions(listView, {
        itemDataSource: dataList.dataSource
    });
}
```

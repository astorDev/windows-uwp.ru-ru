---
author: TylerMSFT
title: Обновление живой плитки из фоновой задачи
description: Используйте фоновую задачу для обновления живой плитки вашего приложения свежим содержимым.
Search.SourceType: Video
ms.assetid: 9237A5BD-F9DE-4B8C-B689-601201BA8B9A
ms.author: twhitney
ms.date: 01/11/2018
ms.topic: article
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
ms.openlocfilehash: 3c379097efaef65357bc1c6b036695ef84671ea6
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5870049"
---
# <a name="update-a-live-tile-from-a-background-task"></a>Обновление живой плитки из фоновой задачи

**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)

Используйте фоновую задачу для обновления живой плитки вашего приложения свежим содержимым.

Ниже представлено видео, демонстрирующее, как добавлять живые плитки в приложения.

<iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Updating-a-live-tile-from-a-background-task/player" width="720" height="405" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="create-the-background-task-project"></a>Создание проекта фоновых задач  

Чтобы задействовать живую плитку в своем приложении, добавьте новый проект компонента среды выполнения Windows в свое решение. Это отдельная сборка, которую операционная система загружает и запускает в фоновом режиме, когда пользователь устанавливает ваше приложение.

1.  В окне обозревателя решений щелкните правой кнопкой мыши решение, выберите команду **Добавить** и нажмите кнопку **Создать проект**.
2.  В диалоговом окне **Добавление нового проекта** выберите шаблон **Компонент среды выполнения Windows** в разделе **Установленные &gt; Другие языки &gt; Visual C# &gt; Windows Universal**.
3.  Назовите проект "BackgroundTasks" и нажмите кнопку **ОК** или коснитесь ее. Microsoft Visual Studio добавит новый проект в решение.
4.  В основном проекте добавьте ссылку на проект BackgroundTasks.

## <a name="implement-the-background-task"></a>Реализация фоновой задачи


Реализуйте интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794), чтобы создать класс, который обновляет живую плитку вашего приложения. Фоновая работа осуществляется в методе Запустить. В этом случае задача получает канал синдикации для блогов MSDN. Чтобы задача преждевременно не закрылась, пока выполняется асинхронный код, получите отсрочку.

1.  В обозревателе решений переименуйте автоматически генерируемый файл, Class1.cs, в BlogFeedBackgroundTask.cs.
2.  В BlogFeedBackgroundTask.cs замените автоматически генерируемый код кодом заглушки для класса **BlogFeedBackgroundTask**.
3.  В реализации метода Run (Запустить) добавьте код для методов **GetMSDNBlogFeed** и **UpdateTile**.

```cs
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

// Added during quickstart
using Windows.ApplicationModel.Background;
using Windows.Data.Xml.Dom;
using Windows.UI.Notifications;
using Windows.Web.Syndication;

namespace BackgroundTasks
{
    public sealed class BlogFeedBackgroundTask  : IBackgroundTask
    {
        public async void Run( IBackgroundTaskInstance taskInstance )
        {
            // Get a deferral, to prevent the task from closing prematurely
            // while asynchronous code is still running.
            BackgroundTaskDeferral deferral = taskInstance.GetDeferral();

            // Download the feed.
            var feed = await GetMSDNBlogFeed();

            // Update the live tile with the feed items.
            UpdateTile( feed );

            // Inform the system that the task is finished.
            deferral.Complete();
        }

        private static async Task<SyndicationFeed> GetMSDNBlogFeed()
        {
            SyndicationFeed feed = null;

            try
            {
                // Create a syndication client that downloads the feed.  
                SyndicationClient client = new SyndicationClient();
                client.BypassCacheOnRetrieve = true;
                client.SetRequestHeader( customHeaderName, customHeaderValue );

                // Download the feed.
                feed = await client.RetrieveFeedAsync( new Uri( feedUrl ) );
            }
            catch( Exception ex )
            {
                Debug.WriteLine( ex.ToString() );
            }

            return feed;
        }

        private static void UpdateTile( SyndicationFeed feed )
        {
            // Create a tile update manager for the specified syndication feed.
            var updater = TileUpdateManager.CreateTileUpdaterForApplication();
            updater.EnableNotificationQueue( true );
            updater.Clear();

            // Keep track of the number feed items that get tile notifications.
            int itemCount = 0;

            // Create a tile notification for each feed item.
            foreach( var item in feed.Items )
            {
                XmlDocument tileXml = TileUpdateManager.GetTemplateContent( TileTemplateType.TileWideText03 );

                var title = item.Title;
                string titleText = title.Text == null ? String.Empty : title.Text;
                tileXml.GetElementsByTagName( textElementName )[0].InnerText = titleText;

                // Create a new tile notification.
                updater.Update( new TileNotification( tileXml ) );

                // Don't create more than 5 notifications.
                if( itemCount++ > 5 ) break;
            }
        }

        // Although most HTTP servers do not require User-Agent header, others will reject the request or return
        // a different response if this header is missing. Use SetRequestHeader() to add custom headers.
        static string customHeaderName = "User-Agent";
        static string customHeaderValue = "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";

        static string textElementName = "text";
        static string feedUrl = @"http://blogs.msdn.com/b/MainFeed.aspx?Type=BlogsOnly";
    }
}
```

## <a name="set-up-the-package-manifest"></a>Настройка манифеста пакета


Чтобы настроить манифест пакета, откройте его и добавьте новую декларацию фоновой задачи. Установите точку входа для задачи на имя класса, включая ее пространство имен.

1.  В средстве "Обозреватель решений" откройте файл Package.appxmanifest.
2.  Щелкните вкладку **Declarations** (Декларации) или коснитесь ее.
3.  В списке **Available Declarations** (Доступные декларации) выберите **BackgroundTasks** и щелкните **Add** (Добавить). Visual Studio добавит **BackgroundTasks** в раздел **Supported Declarations** (Поддерживаемые декларации).
4.  В списке **Supported task types** (Поддерживаемые типы задач) убедитесь, что установлен флажок **Timer** (Таймер).
5.  В разделе **App settings** (Параметры приложений) установите точку входа на **BackgroundTasks.BlogFeedBackgroundTask**.
6.  Щелкните вкладку **Application UI** (Пользовательский интерфейс приложения) или коснитесь ее.
7.  Установите для параметра **Lock screen notifications** (Уведомления экрана блокировки) значение **Badge and Tile Text** (Индикатор событий и текст плитки).
8.  Укажите путь к значку размером 24x24 пикселя в поле **Badge logo** (Индикатор событий).
    **Важные**этом значке должны быть использованы только монохромные и прозрачные пиксели.
9.  В поле **Small logo** (Мелкий значок) укажите путь к значку размером 30x30 пикселей.
10. В поле **Wide logo** (Широкий значок) укажите путь к значку размером 310x150 пикселей.

## <a name="register-the-background-task"></a>Регистрация фоновой задачи


Создайте [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768), чтобы зарегистрировать свою задачу.

> **Примечание**начиная с Windows8.1, параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Если фоновая задача не прошла регистрацию, приложение должно выполнить соответствующее действие— например, применить условный оператор для проверки наличия ошибок регистрации и затем повторить попытку регистрации с другими значениями параметров.
 

На главной странице приложения добавьте метод **RegisterBackgroundTask** и вызовите его в обработчике событий **OnNavigatedTo**.

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Windows.ApplicationModel.Background;
using Windows.Data.Xml.Dom;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;
using Windows.Web.Syndication;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/p/?LinkID=234238

namespace ContosoApp
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.  The Parameter
        /// property is typically used to configure the page.</param>
        protected override void OnNavigatedTo( NavigationEventArgs e )
        {
            this.RegisterBackgroundTask();
        }


        private async void RegisterBackgroundTask()
        {
            var backgroundAccessStatus = await BackgroundExecutionManager.RequestAccessAsync();
            if( backgroundAccessStatus == BackgroundAccessStatus.AllowedMayUseActiveRealTimeConnectivity ||
                backgroundAccessStatus == BackgroundAccessStatus.AllowedWithAlwaysOnRealTimeConnectivity )
            {
                foreach( var task in BackgroundTaskRegistration.AllTasks )
                {
                    if( task.Value.Name == taskName )
                    {
                        task.Value.Unregister( true );
                    }
                }

                BackgroundTaskBuilder taskBuilder = new BackgroundTaskBuilder();
                taskBuilder.Name = taskName;
                taskBuilder.TaskEntryPoint = taskEntryPoint;
                taskBuilder.SetTrigger( new TimeTrigger( 15, false ) );
                var registration = taskBuilder.Register();
            }
        }

        private const string taskName = "BlogFeedBackgroundTask";
        private const string taskEntryPoint = "BackgroundTasks.BlogFeedBackgroundTask";
    }
}
```

## <a name="debug-the-background-task"></a>Отладка фоновой задачи


Чтобы отладить фоновую задачу, установите точку останова в методе Run задачи. На панели инструментов **Место отладки** выберите свою фоновую задачу. В результате система вызовет метод Run немедленно.

1.  Установите точку останова в методе Run задачи.
2.  Нажмите клавишу F5 или выберите элемент **Debug &gt; Start Debugging** (Отладка &gt; Начать отладку), чтобы развернуть и запустить приложение.
3.  После запуска приложения переключитесь снова на Visual Studio.
4.  Убедитесь, что панель инструментов **Debug Location** (Место отладки) видна. Она находится в меню **Вид &gt; Панели инструментов**.
5.  На панели инструментов **Debug Location** (Место отладки) щелкните раскрывающийся список **Suspend** (Приостановить) и выберите **BlogFeedBackgroundTask**.
6.  Visual Studio приостановит выполнение в точке останова.
7.  Нажмите клавишу F5 или выберите элемент **Debug &gt; Continue** (Отладка &gt; Продолжить), чтобы продолжить выполнение приложения.
8.  Для остановки отладки нажмите клавиши SHIFT+F5 или выберите элемент **Debug &gt; Stop Debugging** (Отладка &gt; Остановить отладку).
9.  Вернитесь к плитке приложения на начальном экране. Через несколько секунд на плитке вашего приложения появится уведомление.

## <a name="related-topics"></a>Связанные разделы


* [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
* [**TileUpdateManager**](https://msdn.microsoft.com/library/windows/apps/br208622)
* [**TileNotification**](https://msdn.microsoft.com/library/windows/apps/br208616)
* [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)
* [Руководство и контрольный список по плиткам и индикаторам событий](https://msdn.microsoft.com/library/windows/apps/hh465403)

 

 

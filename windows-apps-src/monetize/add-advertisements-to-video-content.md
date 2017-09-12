---
author: mcleanbyron
ms.assetid: cc24ba75-a185-4488-b70c-fd4078bc4206
description: "Узнайте, как использовать класс AdScheduler для показа рекламы в видеосодержимом."
title: "Показ рекламы в видеосодержимом"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, реклама, рекламные материалы, видео, планировщик, javascript"
ms.openlocfilehash: 834547db2d58291e3bbf75a738d9775e25fbb8e5
ms.sourcegitcommit: 9d1ca16a7edcbbcae03fad50a4a10183a319c63a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2017
---
# <a name="show-ads-in-video-content"></a>Показ рекламы в видеосодержимом


В этом пошаговом руководстве показано, как использовать класс [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) для показа рекламы в видеосодержимом в приложении универсальной платформы Windows (UWP), написанном с использованием JavaScript и HTML.

> [!NOTE]
> Сейчас эта возможность поддерживается только для приложений UWP, написанных с использованием JavaScript и HTML.

Класс [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) работает с прогрессивным и потоковым мультимедиа и использует форматы полезной нагрузки VAST 2.0/3.0 и VMAP. Благодаря использованию стандартов [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) не зависит от рекламной службы, с которой он взаимодействует.

Реклама для видеосодержимое зависит от того, превышает ли длительность программа 10 минут (длинная форма) или нет (короткая форма). Хотя в первом случае настроить службу немного сложнее, в написании клиентского кода различий фактически нет. Если [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) получает полезные данные VAST с одной рекламой вместо манифеста, они рассматриваются как манифест, который вызывается для одного предварительного рекламного ролика (один разрыв в 00:00).

## <a name="prerequisites"></a>Необходимые компоненты

* Установка [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) с помощью Visual Studio 2015 или более поздней версии.

* В проекте необходимо использовать элемент управления [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview), чтобы обслуживать видеосодержимое, в котором планируется показывать рекламу. Этот элемент управления доступен в коллекции библиотек [TVHelpers](https://github.com/Microsoft/TVHelpers) от корпорации Майкрософт на портале GitHub.

  Ниже показано, как объявить [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview) в разметке HTML. Как правило, эта разметка размещена в разделе `<body>` файла index.html (или другого HTML-файла, в зависимости от конкретного проекта).

  ``` html
  <div id="MediaPlayerDiv" data-win-control="TVJS.MediaPlayer">
    <video src="URL to your content">
    </video>
  </div>
  ```

  В следующем примере показано, как настроить [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview) в коде JavaScript.

  [!code-javascript[TrialVersion](./code/AdvertisingSamples/AdSchedulerSamples/js/js/main.js#Snippet1)]

## <a name="how-to-use-the-adscheduler-class-in-your-code"></a>Использование класса AdScheduler в коде

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3. Добавьте в проект ссылку на библиотеку **Microsoft Advertising SDK для JavaScript**.

  а. В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

  б. В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для JavaScript** (версия 10.0).

  в. В **диспетчере ссылок** нажмите "ОК".

4.  Добавьте файл AdScheduler.js в проект:

  а.  В Visual Studio щелкните **Проект** и выберите параметр **Управление пакетами NuGet**.

  б.  В поле поиска введите **Microsoft.StoreServices.VideoAdScheduler** и установите пакет Microsoft.StoreServices.VideoAdScheduler. Файл AdScheduler.js будет добавлен в подкаталог ../js в проекте.

5.  Откройте файл index.html (или другой html-файл, в зависимости от конкретного проекта). В разделе `<head>` после ссылок на JavaScript проекта из default.css и main.js добавьте ссылку на ad.js и adscheduler.js.

  ``` html
  <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
  <script src="/js/adscheduler.js"></script>
  ```

  <span/>
  > **Примечание.**&nbsp;&nbsp;Эта строка должна располагаться в разделе `<head>` после включения main.js; в противном случае при сборке проекта будет выдано сообщение об ошибке.

6.  В файле main.js проекта добавьте код, который создает объект [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx). Передайте объект **MediaPlayer**, в котором размещено видеосодержимое. Код должен быть размещен так, чтобы он выполнялся после [WinJS.UI.processAll](https://msdn.microsoft.com/library/windows/apps/hh440975.aspx).

  [!code-javascript[TrialVersion](./code/AdvertisingSamples/AdSchedulerSamples/js/js/main.js#Snippet2)]

7.  Используйте метод [requestSchedule](https://msdn.microsoft.com/library/windows/apps/mt732208.aspx) или [requestScheduleByUrl](https://msdn.microsoft.com/library/windows/apps/mt732210.aspx) для запроса расписания рекламы с сервера и его вставки во временную шкалу **MediaPlayer**, а затем воспроизведите видео.

  * Если вы — партнер корпорации Майкрософт, который получил разрешение на запрос расписания рекламы с сервера Майкрософт, используйте [requestSchedule](https://msdn.microsoft.com/library/windows/apps/mt732208.aspx) и укажите идентификатор приложения и идентификатор рекламного блока, предоставленные Майкрософт. Этот метод принимает форму **обещания**, т. е. асинхронной конструкции, которой передаются два указателя на функции для обработки успешных и ошибочных случаев. Подробнее см. в разделе [Асинхронные шаблоны в UWP на JavaScript](https://msdn.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps#asynchronous-patterns-in-uwp-using-javascript).

      [!code-javascript[TrialVersion](./code/AdvertisingSamples/AdSchedulerSamples/js/js/main.js#Snippet3)]

  * Для запроса расписания рекламы от стороннего сервера используйте [requestScheduleByUrl](https://msdn.microsoft.com/library/windows/apps/mt732210.aspx) и передайте URL-адрес сервера. Этот метод также принимает форму **обещания**.

      [!code-javascript[TrialVersion](./code/AdvertisingSamples/AdSchedulerSamples/js/js/main.js#Snippet4)]

    <span/>
    >**Примечание.**&nbsp;&nbsp;Необходимо вызывать метод **play**, даже если функция завершится ошибкой, так как [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) сообщит **MediaPlayer** о необходимости пропустить рекламу и сразу перейти к содержимому. Могут существовать другие бизнес-требования, например вставка встроенной рекламы, если удаленно получить рекламное объявление не удалось.

8.  Во время воспроизведения можно обрабатывать дополнительные события, которые позволяют приложению отслеживать ход выполнения, и (или) ошибки, которые могут возникнуть после начального процесса сопоставления рекламы. В следующем примере кода показаны некоторые из этих событий, в том числе [onPodStart](https://msdn.microsoft.com/library/windows/apps/mt732206.aspx), [onPodEnd](https://msdn.microsoft.com/library/windows/apps/mt732205.aspx), [onPodCountdown](https://msdn.microsoft.com/library/windows/apps/mt732204.aspx), [onAdProgress](https://msdn.microsoft.com/library/windows/apps/mt732201.aspx), [onAllComplete](https://msdn.microsoft.com/library/windows/apps/mt732202.aspx) и [onErrorOccurred](https://msdn.microsoft.com/library/windows/apps/mt732203.aspx).

  [!code-javascript[TrialVersion](./code/AdvertisingSamples/AdSchedulerSamples/js/js/main.js#Snippet5)]

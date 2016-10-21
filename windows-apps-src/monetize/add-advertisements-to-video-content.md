---
author: mcleanbyron
ms.assetid: cc24ba75-a185-4488-b70c-fd4078bc4206
description: "Узнайте, как использовать класс AdScheduler для добавления рекламы в видео."
title: "Добавление рекламы в видеосодержимое в HTML 5 и JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: 5f975d0a99539292e1ce91ca09dbd5fac11c4a49
ms.openlocfilehash: 5fd097d978b1960ed957a7e12ab7eece012c5b41

---

# Добавление рекламы в видеосодержимое в HTML 5 и JavaScript


В этом пошаговом руководстве показано, как использовать класс [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) для добавления рекламы в видеосодержимое в приложение универсальной платформы Windows (UWP), написанное с использованием JavaScript и HTML.

>**Примечание.**&nbsp;&nbsp;Сейчас эта возможность поддерживается только для приложений UWP, написанных с использованием JavaScript и HTML.

Класс [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx)работает с прогрессивным и потоковым мультимедиа и использует форматы полезной нагрузки VAST 2.0/3.0 и VMAP. Благодаря использованию стандартов [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) не зависит от рекламной службы, с которой он взаимодействует.

Реклама для видеосодержимое зависит от того, превышает ли длительность программа 10 минут (длинная форма) или нет (короткая форма). Хотя в первом случае настроить службу немного сложнее, в написании клиентского кода различий фактически нет. Если [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) получает полезные данные VAST с одной рекламой вместо манифеста, они рассматриваются как манифест, который вызывается для одного предварительного рекламного ролика (один разрыв в 00:00).

## Необходимые компоненты

* Установите [пакет SDK служб Microsoft Store](http://aka.ms/store-em-sdk) с Visual Studio 2015.

* В проекте необходимо использовать элемент управления [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview), чтобы обслуживать видеосодержимое, в котором планируется показывать рекламу. Этот элемент управления доступен в коллекции библиотек [TVHelpers](https://github.com/Microsoft/TVHelpers) от корпорации Майкрософт на портале GitHub.

  Ниже показано, как объявить [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview) в разметке HTML. Как правило, эта разметка размещена в разделе `<body>` файла default.html (или другого HTML-файл, если это необходимо для вашего проекта).
  ``` html
  <div id="MediaPlayerDiv" data-win-control="TVJS.MediaPlayer">
    <video src="URL to your content">
    </video>
  </div>
  ```

  В следующем примере показано, как настроить [MediaPlayer](https://github.com/Microsoft/TVHelpers/wiki/MediaPlayer-Overview) в коде JavaScript.
  ``` javascript
  var mediaPlayerDiv = document.createElement("div");
  document.body.appendChild(mediaPlayerDiv);

  var videoElement = document.createElement("video");
  videoElement.src = "<URL to your content>";
  mediaPlayerDiv.appendChild(videoElement);

  var mediaPlayer = new TVJS.MediaPlayer(mediaPlayerDiv);
  ```

## Использование класса AdScheduler в коде

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3. Добавьте в проект ссылку на библиотеку **Microsoft Advertising SDK для JavaScript**.

  а. В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

  б. В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для JavaScript** (версия 10.0).

  в. В **диспетчере ссылок** нажмите "ОК".

4.  Добавьте файл AdScheduler.js в проект:

  а.  В Visual Studio щелкните **Проект** и выберите параметр **Управление пакетами NuGet**.

  б.  В поле поиска введите **Microsoft.StoreServices.VideoAdScheduler** и установите пакет Microsoft.StoreServices.VideoAdScheduler. Файл AdScheduler.js будет добавлен в подкаталог ../js в проекте.

5.  Откройте файл default.html (или другой html-файл, если это необходимо для вашего проекта). В разделе `<head>` после ссылок на JavaScript проекта из default.css и default.js добавьте ссылку на ad.js и adscheduler.js.

    ``` html
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
    <script src="/js/adscheduler.js"></script>
    ```

    > **Примечание**. Эта строка должна располагаться в разделе `<head>` после включения default.js; в противном случае будет выдано сообщение об ошибке при сборке проекта.

6.  В файле default.js проекта добавьте код, который создает объект [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx). Передайте объект **MediaPlayer**, в котором размещено видеосодержимое. Код должен быть размещен так, чтобы он выполнялся после [WinJS.UI.processAll](https://msdn.microsoft.com/library/windows/apps/hh440975.aspx).

    ``` javascript
    var myMediaPlayer = document.getElementById("MediaPlayerDiv");
    var myAdScheduler = new MicrosoftNSJS.Advertising.AdScheduler(myMediaPlayer);
    ```

7.  Используйте методы [requestSchedule](https://msdn.microsoft.com/library/windows/apps/mt732208.aspx) или [requestScheduleByUrl](https://msdn.microsoft.com/library/windows/apps/mt732210.aspx) для запроса расписания рекламы с сервера и его вставки во временную шкалу **MediaPlayer**, а затем воспроизведите видео.

  * Если вы — партнер корпорации Майкрософт, который получил разрешение на запрос расписания рекламы с сервера Майкрософт, используйте [requestSchedule](https://msdn.microsoft.com/library/windows/apps/mt732208.aspx) и укажите идентификатор приложения и идентификатор рекламного блока, предоставленные Майкрософт. Этот метод принимает форму **обещания**, т. е. асинхронной конструкции, которой передаются два указателя на функции для обработки успешных и ошибочных случаев. Подробнее см. в разделе [Асинхронные шаблоны в UWP на JavaScript](https://msdn.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps#asynchronous-patterns-in-uwp-using-javascript).

      ``` javascript
  myAdScheduler.requestSchedule("your application ID", "your ad unit ID").then(
        function promiseSuccessHandler(schedule) {
           // Success: play the video content with the scheduled ads.
           myMediaPlayer.tvControl.play();
        },
        function promiseErrorHandler(err) {
           // Error: play the video content without the ads.
           myMediaPlayer.tvControl.play();
        });
  ```

  * Для запроса расписания рекламы от стороннего сервера используйте [requestScheduleByUrl](https://msdn.microsoft.com/library/windows/apps/mt732210.aspx)и передайте URL-адрес сервера. Этот метод также принимает форму **обещания**.

      ``` javascript
  myAdScheduler.requestScheduleByUrl("your URL").then(
        function promiseSuccessHandler(schedule) {
            // Success: play the video content with the scheduled ads.
            myMediaPlayer.winControl.play();
        },
        function promiseErrorHandler(evt) {
            // Error: play the video content without the ads.
             myMediaPlayer.winControl.play();
        });
  ```

  >**Примечание.**&nbsp;&nbsp;Вам необходимо вызывать метод **play**, даже если функция завершится ошибкой, так как [AdScheduler](https://msdn.microsoft.com/library/windows/apps/mt732197.aspx) сообщит **MediaPlayer** о необходимости пропустить рекламу и сразу перейти к содержимому. Могут существовать другие бизнес-требования, например вставка встроенной рекламы, если удаленно получить рекламное объявление не удалось.

8.  Во время воспроизведения существуют дополнительные события, которые позволяют приложению отслеживать ход выполнения и ошибки, которые могут возникнуть после начального процесс сопоставления рекламы. В следующем примере кода показаны некоторые из этих событий.
  * [onPodStart](https://msdn.microsoft.com/library/windows/apps/mt732206.aspx):

      ```javascript
      // Raised when an ad pod starts. Make the countdown timer visible.
      myAdScheduler.onPodStart = function (sender, data) {
          myCounterDiv.style.visibility= "visible";
      }  
      ```

  * [onPodEnd](https://msdn.microsoft.com/library/windows/apps/mt732205.aspx):

      ```javascript
      // Raised when an ad pod ends. Hide the countdown timer.
      myAdScheduler.onPodEnd = function (sender, data) {
          myCounterDiv.style.visibility= "hidden";
      }
      ```

  * [onPodCountdown](https://msdn.microsoft.com/library/windows/apps/mt732204.aspx):

      ```javascript
      // Raised when an ad is playing and indicates how many seconds remain in the current pod of ads. This is useful when the app wants to show a visual countdown until the video content resumes.
      myAdScheduler.onPodCountdown = function (sender, data) {
            myCounterText     = "Content resumes in: " +
            Math.ceil(data.remainingPodTime) + " seconds.";
      }  
      ```

  * [onAdProgress](https://msdn.microsoft.com/library/windows/apps/mt732201.aspx):

      ```javascript
      // Raised during each quartile of progress through the ad clip.
      myAdScheduler.onAdProgress = function (sender, data) {
      }
      ```

  * [onAllComplete](https://msdn.microsoft.com/library/windows/apps/mt732202.aspx):

      ```javascript
      // Raised when the ads and content are complete.
      myAdScheduler.onAllComplete = function (sender) {
      }
      ```

  * [onErrorOccurred](https://msdn.microsoft.com/library/windows/apps/mt732203.aspx):

      ```javascript
      // Raised when an error occurs during playback after successfully scheduling.
      myAdScheduler.onErrorOccurred = function (sender, data) {
      }
      ```



<!--HONumber=Aug16_HO5-->



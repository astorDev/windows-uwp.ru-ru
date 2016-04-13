---
title: Объявление фоновых задач в манифесте приложения
description: Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения.
ms.assetid: 6B4DD3F8-3C24-4692-9084-40999A37A200
---

# Объявление фоновых задач в манифесте приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Схема BackgroundTasks**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)

Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения.

Необходимо объявлять фоновые задачи в манифесте приложения, иначе приложение не сможет регистрировать их (будет возникать исключение). Кроме того, это необходимо, чтобы приложение прошло сертификацию.

В этой статье предполагается, что вы создали один или несколько классов фоновых задач и ваше приложение регистрирует каждую запускаемую фоновую задачу в ответ на срабатывание хотя бы одного триггера.

## Добавление расширений вручную


Откройте манифест приложения (Package.appxmanifest) и перейдите к элементу Application. Создайте элемент Extensions (если он еще не существует).

Указанный ниже фрагмент кода взят из [примера фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

```xml
<Application Id="App"
   ...
   <Extensions>
     <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.SampleBackgroundTask">
       <BackgroundTasks>
         <Task Type="systemEvent" />
         <Task Type="timer" />
       </BackgroundTasks>
     </Extension>
     <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.ServicingComplete">
       <BackgroundTasks>
         <Task Type="systemEvent"/>
       </BackgroundTasks>
     </Extension>
   </Extensions>
 </Application>
```

## Добавление расширения фоновой задачи


Объявите свою первую фоновую задачу.

Скопируйте этот код в элемент Extensions (атрибуты будут добавлены на следующих этапах).

```xml
      <Extensions>
        <Extension Category="windows.backgroundTasks" EntryPoint="">
          <BackgroundTasks>
            <Task Type="" />
          </BackgroundTasks>
        </Extension>
      </Extensions>
```

1.  Измените атрибут EntryPoint, включив в него строку, которая используется в вашем коде как точка входа при регистрации фоновой задачи (**namespace.classname**).

    В этом примере точкой входа является ExampleBackgroundTaskNameSpace.ExampleBackgroundTaskClassName:

    ```xml
          <Extensions>
            <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.ExampleBackgroundTaskClassName">
              <BackgroundTasks>
                <Task Type="" />
              </BackgroundTasks>
            </Extension>
          </Extensions>
    ```

2.  Измените список атрибута Type элемента Task, указав в нем тип регистрации задач, используемый с этой фоновой задачей. Если фоновая задача зарегистрирована с несколькими типами триггеров, добавьте дополнительные элементы Task и атрибуты Type для каждого из них.

    **Примечание.** Внесите в список все используемые типы триггеров. В противном случае фоновая задача не будет регистрироваться для необъявленных типов триггеров (метод [**Register**](https://msdn.microsoft.com/library/windows/apps/br224772) не будет выполняться и выдаст исключение).

    Этот пример фрагмента показывает использование триггеров системных событий и push-уведомлений:

    ```xml
                <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.BackgroundTaskClass">
                  <BackgroundTasks>
                    <Task Type="systemEvent" />
                    <Task Type="pushNotification" />
                  </BackgroundTasks>
                </Extension>
                ```

    > **Note**  Normally, an app will run in a special process called "BackgroundTaskHost.exe". It is possible to add an Executable element to the Extension element, allowing the background task to run in the context of the app. Only use the Executable element with background tasks that require it, such as the [**ControlChannelTrigger**](https://msdn.microsoft.com/library/windows/apps/hh701032).    

## Add Additional Background Task Extensions


Repeat step 2 for each additional background task class registered by your app.

The following example is the complete Application element from the [background task sample]( http://go.microsoft.com/fwlink/p/?linkid=227509). This shows the use of 2 background task classes with a total of 3 trigger types. Copy the Extensions section of this example, and modify it as needed, to declare background tasks in your application manifest.

```xml
<Applications>
    <Application Id="App"
      Executable="$targetnametoken$.exe"
      EntryPoint="BackgroundTask.App">
        <uap:VisualElements
          DisplayName="BackgroundTask"
          Square150x150Logo="Assets\StoreLogo-sdk.png"
          Square44x44Logo="Assets\SmallTile-sdk.png"
          Description="BackgroundTask"
          
          BackgroundColor="#00b2f0">
          <uap:LockScreen Notification="badgeAndTileText" BadgeLogo="Assets\smalltile-Windows-sdk.png" />
            <uap:SplashScreen Image="Assets\Splash-sdk.png" />
            <uap:DefaultTile DefaultSize="square150x150Logo" Wide310x150Logo="Assets\tile-sdk.png" >
                <uap:ShowNameOnTiles>
                    <uap:ShowOn Tile="square150x150Logo" />
                    <uap:ShowOn Tile="wide310x150Logo" />
                </uap:ShowNameOnTiles>
            </uap:DefaultTile>
        </uap:VisualElements>

      <Extensions>
        <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.SampleBackgroundTask">
          <BackgroundTasks>
            <Task Type="systemEvent" />
            <Task Type="timer" />
          </BackgroundTasks>
        </Extension>
        <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.ServicingComplete">
          <BackgroundTasks>
            <Task Type="systemEvent"/>
          </BackgroundTasks>
        </Extension>
      </Extensions>
    </Application>
</Applications>
```

## Ссылки по теме

* [Отладка фоновой задачи](debug-a-background-task.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)

 

 





<!--HONumber=Mar16_HO1-->



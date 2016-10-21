---
author: TylerMSFT
title: "Объявление фоновых задач в манифесте приложения"
description: "Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения."
ms.assetid: 6B4DD3F8-3C24-4692-9084-40999A37A200
translationtype: Human Translation
ms.sourcegitcommit: b877ec7a02082cbfeb7cdfd6c66490ec608d9a50
ms.openlocfilehash: 6ec298a956673c114d34d64b026394ece2c33506

---

# Объявление фоновых задач в манифесте приложения


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Схема BackgroundTasks**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)

Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения.

> [!Important]
>  Эта статья относится к фоновым задачам, выполняемым в отдельном процессе. Однопроцессные фоновые задачи не объявляются в манифесте.

Фоновые задачи, выполняемые в отдельном процессе, необходимо объявлять в манифесте приложения, иначе приложение не сможет регистрировать их (будет возникать исключение). Кроме того, это необходимо, чтобы приложение прошло сертификацию.

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


## Добавление дополнительных расширений фоновой задачи

Повторите этап 2 для каждого дополнительного класса фоновой задачи, регистрируемого вашим приложением.

В следующем примере демонстрируется полный элемент Application из [образца фоновой задачи]( http://go.microsoft.com/fwlink/p/?linkid=227509). Здесь показано использование двухклассов фоновых задач с тремятипами триггеров. Чтобы объявить фоновые задачи в манифесте вашего приложения, скопируйте раздел Extensions из этого примера и измените его так, как вам необходимо.

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

## Объявление фоновой задачи для выполнения в другом процессе

Новые возможности в Windows 10 версии 1507 позволяют выполнять фоновую задачу в процессе, отличном от BackgroundTaskHost.exe (процесс, в котором фоновые задачи выполняются по умолчанию).  Есть два пути: выполнение в том же процессе, что и приложение переднего плана, или выполнение в экземпляре BackgroundTaskHost.exe, отделенном от других экземпляров фоновых задач того же приложения.  

### Выполнение в приложении переднего плана

Вот пример XML, в котором объявляется фоновая задача, выполняемая в том же процессе, что и приложение переднего плана. Обратите внимание на атрибут `Executable`:

```xml
<Extensions>
    <Extension Category="windows.backgroundTasks" EntryPoint="ExecModelTestBackgroundTasks.ApplicationTriggerTask" Executable="$targetnametoken$.exe">
        <BackgroundTasks>
            <Task Type="systemEvent" />
        </BackgroundTasks>
    </Extension>
</Extensions>
```

> [!Note]
> Используйте элемент Executable только с теми фоновыми задачами, которым он требуется, например, [**ControlChannelTrigger**](https://msdn.microsoft.com/library/windows/apps/hh701032).  

### Выполнение в другом фоновом хост-процессе

Вот пример XML, в котором объявляется фоновая задача, выполняемая в процессе BackgroundTaskHost.exe, отделенном от других экземпляров фоновых задач того же приложения. Обратите внимание на атрибут `ResourceGroup`, определяющий, какие фоновые задачи будут выполняться вместе.

```xml
<Extensions>
    <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.SessionConnectedTriggerTask" ResourceGroup="foo">
      <BackgroundTasks>
        <Task Type="systemEvent" />
      </BackgroundTasks>
    </Extension>
    <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.TimeZoneTriggerTask" ResourceGroup="foo">
      <BackgroundTasks>
        <Task Type="systemEvent" />
      </BackgroundTasks>
    </Extension>
    <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.TimerTriggerTask" ResourceGroup="bar">
      <BackgroundTasks>
        <Task Type="timer" />
      </BackgroundTasks>
    </Extension>
    <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.ApplicationTriggerTask" ResourceGroup="bar">
      <BackgroundTasks>
        <Task Type="general" />
      </BackgroundTasks>
    </Extension>
    <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.MaintenanceTriggerTask" ResourceGroup="foobar">
      <BackgroundTasks>
        <Task Type="general" />
      </BackgroundTasks>
    </Extension>
</Extensions>
```


## Связанные статьи


* [Отладка фоновой задачи](debug-a-background-task.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)



<!--HONumber=Aug16_HO3-->



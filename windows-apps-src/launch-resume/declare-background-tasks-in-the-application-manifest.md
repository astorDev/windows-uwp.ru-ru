---
author: TylerMSFT
title: Объявление фоновых задач в манифесте приложения
description: Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения.
ms.assetid: 6B4DD3F8-3C24-4692-9084-40999A37A200
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновые задачи
ms.localizationpriority: medium
ms.openlocfilehash: 00e685085c004cced24b9a42ef2261a26eef10bb
ms.sourcegitcommit: 9e2c34a5ed3134aeca7eb9490f05b20eb9a3e5df
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "3986853"
---
# <a name="declare-background-tasks-in-the-application-manifest"></a>Объявление фоновых задач в манифесте приложения




**Важные API**

-   [**Схема BackgroundTasks**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)

Вы можете разрешить использование фоновых задач, объявив их как расширения в манифесте приложения.

> [!Important]
>  Эта статья относится к фоновым задачам, выполняемым вне процесса. Выполняемые внутри процесса фоновые задачи не объявляются в манифесте.

Необходимо объявлять выполняемые вне процесса фоновые задачи в манифесте приложения, иначе приложение не сможет регистрировать их (будет возникать исключение). Кроме того, это объявление необходимо, чтобы манифест приложения прошел сертификацию.

В этой статье предполагается, что вы создали один или несколько классов фоновых задач и ваше приложение регистрирует каждую запускаемую фоновую задачу в ответ на срабатывание хотя бы одного триггера.

## <a name="add-extensions-manually"></a>Добавление расширений вручную


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

## <a name="add-a-background-task-extension"></a>Добавление расширения фоновой задачи  

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

### <a name="add-multiple-background-task-extensions"></a>Добавление нескольких расширений фоновой задачи

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

## <a name="declare-where-your-background-task-will-run"></a>Объявление места выполнения фоновой задачи

Вы можете указать, где будет выполняться фоновая задача:

* По умолчанию они выполняются в процессе BackgroundTaskHost.exe.
* В том же процессе, что и ваше приложение переднего плана.
* Используйте `ResourceGroup` для размещения нескольких фоновых задач в одном и том же процессе размещения или разделения их на различные процессы.
* Используйте `SupportsMultipleInstances` для выполнения фонового процесса в новом процессе, который получает собственные ограничения на ресурсы (память, ЦП) каждый раз, когда активируется новый триггер.

### <a name="run-in-the-same-process-as-your-foreground-application"></a>Выполнение в том же процессе, что и ваше приложение переднего плана

Вот пример XML, в котором объявляется фоновая задача, выполняемая в том же процессе, что и приложение переднего плана.

```xml
<Extensions>
    <Extension Category="windows.backgroundTasks" EntryPoint="ExecModelTestBackgroundTasks.ApplicationTriggerTask">
        <BackgroundTasks>
            <Task Type="systemEvent" />
        </BackgroundTasks>
    </Extension>
</Extensions>
```

Когда вы указываете **EntryPoint**, приложение получает обратный вызов для указанного метода, когда срабатывает триггер. Если вы не указываете **EntryPoint**, приложение получает обратный вызов через [OnBackgroundActivated()](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx).  Дополнительные сведения см. в разделе [Создание и регистрация фоновой задачи в процессе](create-and-register-an-inproc-background-task.md).

### <a name="specify-where-your-background-task-runs-with-the-resourcegroup-attribute"></a>Укажите, где фоновая задача выполняется, с помощью атрибута ResourceGroup.

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

### <a name="run-in-a-new-process-each-time-a-trigger-fires-with-the-supportsmultipleinstances-attribute"></a>Запуск в новом процессе каждый раз, когда активируется  триггер, с помощью атрибута SupportsMultipleInstances

В этом примере объявляется, что фоновая задача, которая выполняется в новом процессе, который получает собственные ограничения на ресурсы (память и ЦП) каждый раз, когда активируется новый триггер. Обратите внимание на использование `SupportsMultipleInstances`, который обеспечивает это поведение. Для использования этого атрибута должны ориентироваться SDK версии "10.0.15063» (создатели обновления Windows 10) или более поздней версии.

```xml
<Package
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    ...
    <Applications>
        <Application ...>
            ...
            <Extensions>
                <Extension Category="windows.backgroundTasks" EntryPoint="BackgroundTasks.TimerTriggerTask">
                    <BackgroundTasks uap4:SupportsMultipleInstances=“True”>
                        <Task Type="timer" />
                    </BackgroundTasks>
                </Extension>
            </Extensions>
        </Application>
    </Applications>
```

> [!NOTE]
> Вы не можете задать `ResourceGroup` или `ServerName` в сочетании с `SupportsMultipleInstances`.

## <a name="related-topics"></a>Статьи по теме

* [Отладка фоновой задачи](debug-a-background-task.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)

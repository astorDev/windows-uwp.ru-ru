---
title: Создание универсального приложения для Windows с несколькими экземплярами
description: В этом разделе приводится методика создания приложений UWP, поддерживающих запуск нескольких экземпляров.
keywords: UWP с несколькими экземплярами
ms.date: 09/21/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 9be9b5eec70bc98bc2c44beaf1dcfbba00876f20
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259439"
---
# <a name="create-a-multi-instance-universal-windows-app"></a>Создание универсального приложения для Windows с несколькими экземплярами

В этом разделе приводится методика создания приложений универсальной платформы Windows (UWP) с несколькими экземплярами.

Из Windows 10 версии 1803 (10,0; Сборка 17134). приложение UWP может участвовать в поддержке нескольких экземпляров. Если запущен экземпляр приложения UWP с несколькими экземплярами, при последующих запросах на активацию платформа не будет активировать существующий экземпляр. Вместо этого будет создан новый экземпляр, выполняемый в отдельном процессе.

> [!IMPORTANT]
> Множественное создание экземпляров поддерживается для приложений JavaScript, но перенаправление нескольких экземпляров — нет. Так как перенаправление с множественным созданием экземпляров не поддерживается для приложений JavaScript, класс [**аппинстанце**](/uwp/api/windows.applicationmodel.appinstance) не полезен для таких приложений.

## <a name="opt-in-to-multi-instance-behavior"></a>Согласие на работу с несколькими экземплярами

При создании нового приложения с несколькими экземплярами можно установить шаблоны [Multi-Instance App Project Templates.VSIX](https://marketplace.visualstudio.com/items?itemName=AndrewWhitechapelMSFT.MultiInstanceApps), доступные в [Visual Studio Marketplace](https://marketplace.visualstudio.com/). После установки шаблонов они будут доступны в диалоговом окне **Новый проект** в разделе **Visual C# > Windows Universal** (или **Другие языки > Visual C++ > Visual C# > Windows Universal**).

Устанавливаются два шаблона: **Приложение UWP с несколькими экземплярами**, содержащее шаблон для создания приложения c несколькими экземплярами, и **Приложение UWP с несколькими экземплярами и перенаправлением**, обеспечивающее дополнительную логику, на основе которой можно либо запускать новый экземпляр, либо выборочно активировать экземпляр, который уже был запущен. Например, может потребоваться, чтобы только один экземпляр был изменен в одном и том же документе, поэтому экземпляр, на котором этот файл открыт, открывается на переднем плане, а не запускается новый экземпляр.

Оба шаблона добавляют `SupportsMultipleInstances` в файл `package.appxmanifest`. Обратите внимание на префикс пространства имен `desktop4` и `iot2`: только проекты, предназначенные для проектов классических или "Интернет вещей" (IoT), поддерживают создание нескольких экземпляров.

```xml
<Package
  ...
  xmlns:desktop4="http://schemas.microsoft.com/appx/manifest/desktop/windows10/4"
  xmlns:iot2="http://schemas.microsoft.com/appx/manifest/iot/windows10/2"  
  IgnorableNamespaces="uap mp desktop4 iot2">
  ...
  <Applications>
    <Application Id="App"
      ...
      desktop4:SupportsMultipleInstances="true"
      iot2:SupportsMultipleInstances="true">
      ...
    </Application>
  </Applications>
   ...
</Package>
```

## <a name="multi-instance-activation-redirection"></a>Несколько экземпляров с активацией перенаправлением

 Поддержка нескольких экземпляров для приложений UWP выходит за рамки простого обеспечения возможности запускать несколько экземпляров приложения. Она позволяет отладить работу в случаях, когда вы хотите решить, запускать ли новый экземпляр приложения или активировать экземпляр, который уже выполняется. Например, при запуске приложения для изменения файла, который уже редактируется в другом экземпляре, может потребоваться выполнить перенаправление с активацией этого экземпляра, а не открыть другой экземпляр для редактирования файла.

Чтобы увидеть это в действии, просмотрите это видео о создании приложений универсальной платформы Windows с несколькими экземплярами.

> [!VIDEO https://www.youtube.com/embed/clnnf4cigd0]

Шаблон **Приложения UWP с несколькими экземплярами и перенаправлением** добавляет `SupportsMultipleInstances` в файл package.appxmanifest, как показано выше, а также добавляет **Program.cs** (или **Program.cpp**, если вы используете версию шаблона для C++) в ваш проект, содержащий функцию `Main()`. Логика перенаправления с активацией выполняется в функции `Main`. Ниже приведен шаблон для **Program.CS** .

Свойство [**аппинстанце. рекоммендединстанце**](/uwp/api/windows.applicationmodel.appinstance.recommendedinstance) представляет предпочитаемый оболочкой экземпляр для этого запроса активации, если таковой имеется (или `null`, если он отсутствует). Если оболочка предоставляет предпочтение, можно перенаправить активацию на этот экземпляр или игнорировать его при выборе.

``` csharp
public static class Program
{
    // This example code shows how you could implement the required Main method to
    // support multi-instance redirection. The minimum requirement is to call
    // Application.Start with a new App object. Beyond that, you may delete the
    // rest of the example code and replace it with your custom code if you wish.

    static void Main(string[] args)
    {
        // First, we'll get our activation event args, which are typically richer
        // than the incoming command-line args. We can use these in our app-defined
        // logic for generating the key for this instance.
        IActivatedEventArgs activatedArgs = AppInstance.GetActivatedEventArgs();

        // If the Windows shell indicates a recommended instance, then
        // the app can choose to redirect this activation to that instance instead.
        if (AppInstance.RecommendedInstance != null)
        {
            AppInstance.RecommendedInstance.RedirectActivationTo();
        }
        else
        {
            // Define a key for this instance, based on some app-specific logic.
            // If the key is always unique, then the app will never redirect.
            // If the key is always non-unique, then the app will always redirect
            // to the first instance. In practice, the app should produce a key
            // that is sometimes unique and sometimes not, depending on its own needs.
            string key = Guid.NewGuid().ToString(); // always unique.
                                                    //string key = "Some-App-Defined-Key"; // never unique.
            var instance = AppInstance.FindOrRegisterInstanceForKey(key);
            if (instance.IsCurrentInstance)
            {
                // If we successfully registered this instance, we can now just
                // go ahead and do normal XAML initialization.
                global::Windows.UI.Xaml.Application.Start((p) => new App());
            }
            else
            {
                // Some other instance has registered for this key, so we'll 
                // redirect this activation to that instance instead.
                instance.RedirectActivationTo();
            }
        }
    }
}
```

в первую очередь выполняется `Main()`. Он выполняется перед [**запуском**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application#Windows_UI_Xaml_Application_OnLaunched_Windows_ApplicationModel_Activation_LaunchActivatedEventArgs_) и [**активируется**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application#Windows_UI_Xaml_Application_OnActivated_Windows_ApplicationModel_Activation_IActivatedEventArgs_). Это позволяет вам определить, следует ли активировать этот или другой экземпляр, перед выполнением какого-либо кода инициализации в вашем приложении.

Приведенный выше код определяет, какой экземпляр вашего приложения активируется: существующий или новый. Для определения наличия существующего экземпляра, который требуется активировать, используется ключ. Например, если ваше приложение может запускаться для [Обработки активации файла](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/handle-file-activation), в качестве ключа можно использовать имя файла. Затем можно проверить, зарегистрирован ли экземпляр вашего приложения с таким ключом, и активировать его вместо открытия нового экземпляра. Это идея кода: `var instance = AppInstance.FindOrRegisterInstanceForKey(key);`

Если обнаруживается зарегистрированный экземпляр с таким ключом, этот экземпляр активируется. Если ключ не найден, текущий экземпляр (в котором в настоящее время выполняется `Main`) создает объект приложения и запускается.

## <a name="background-tasks-and-multi-instancing"></a>Фоновые задачи и многоэкземплярность.

- Выполняемые вне процесса фоновые задачи поддерживают создание нескольких экземпляров. Как правило, каждый новый триггер приводит к созданию нового экземпляра класса фоновой задачи (хотя с технической точки зрения несколько фоновых задач могут выполняться в одном процессе). Тем не менее, создается другой экземпляр фоновой задачи.
- Выполняемые внутри процесса фоновые задачи не поддерживают создание нескольких экземпляров.
- Фоновые задачи с аудиоданными не поддерживают создание нескольких экземпляров.
- Когда приложение регистрирует фоновую задачу, оно обычно сначала проверяет, зарегистрирована ли уже такая задача, и затем удаляет или повторно регистрирует ее либо не выполняет никаких действий, чтобы сохранить существующую регистрацию. Это по-прежнему обычное поведение для нескольких экземпляров приложения. Тем не менее приложение с несколькими экземплярами может при необходимости зарегистрировать отдельное имя фоновой задачи для каждого экземпляра. Это приведет к нескольким регистрациям для одного триггера, и несколько экземпляров фоновых задач активируются, когда триггер срабатывает.
- Службы приложений запускают отдельный экземпляр фоновой задачи службы приложения для каждого подключения. Это остается неизменным для приложений с несколькими экземплярами, то есть каждый экземпляр многоэкземплярного приложения получит собственный экземпляр фоновой задачи службы приложения. 

## <a name="additional-considerations"></a>Дополнительные рекомендации

- Поддержка нескольких экземпляров поддерживается приложениями UWP в проектах, предназначенных для настольных ПК и Интернета вещей (IoT).
- Во избежание состояний гонки и конфликтов многоэкземплярным приложениям необходимо выполнять действия по синхронизации и разделению доступа к параметрам, локальному хранилищу приложения и другим ресурсам (например, файлам пользователей, хранилищу данных и т. д.), которые могут совместно использоваться несколькими экземплярами. Доступны стандартные механизмы синхронизации, такие как мьютексы, семафоры, события и т. д.
- Если приложение содержит `SupportsMultipleInstances` в файле Package.appxmanifest, в его расширениях не нужно объявлять `SupportsMultipleInstances`. 
- Если вы добавляете `SupportsMultipleInstances` в другие расширения помимо фоновых задач и служб приложения, а приложение, в котором размещается расширение, также не объявляет `SupportsMultipleInstances` в своем файле Package.appxmanifest, возникает ошибка схемы.
- Приложения могут использовать объявление [**ResourceGroup**](https://docs.microsoft.com/windows/uwp/launch-resume/declare-background-tasks-in-the-application-manifest) в своем манифесте для группирования нескольких фоновых задач в один узел. Это конфликтует с моделью с несколькими экземплярами, где каждая активация переходит в отдельный хост-процесс. Поэтому приложения не могут объявлять и `SupportsMultipleInstances`, и `ResourceGroup` в манифесте.

## <a name="sample"></a>Пример

Пример перенаправления активации с несколькими экземплярами см. в разделе [пример с несколькими экземплярами](https://github.com/Microsoft/AppModelSamples/tree/master/Samples/BananaEdit) .

## <a name="see-also"></a>См. также

[AppInstance.FindOrRegisterInstanceForKey](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_FindOrRegisterInstanceForKey_System_String_)
[AppInstance.GetActivatedEventArgs](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_GetActivatedEventArgs)
[AppInstance.RedirectActivationTo](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_RedirectActivationTo)
[Обработка активации приложения](https://docs.microsoft.com/windows/uwp/launch-resume/activate-an-app)

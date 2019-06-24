---
title: Запуск приложения по умолчанию для файла
description: Узнайте, как запускать приложение по умолчанию для файла.
ms.assetid: BB45FCAF-DF93-4C99-A8B5-59B799C7BD98
ms.date: 07/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 94011a50bd339b98b6bb77ff82f5863d8c89c603
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318724"
---
# <a name="launch-the-default-app-for-a-file"></a>Запуск приложения по умолчанию для файла

**Важные API**

-   [**Windows.System.Launcher.LaunchFileAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync)

Узнайте, как запускать приложение по умолчанию для файла. Многим приложениям необходимо работать с файлами, которые они не могут обрабатывать самостоятельно. Например, почтовые приложения получают файлы разных типов, и им требуется способ запуска этих файлов в обработчиках по умолчанию. Далее показано, как использовать API [**Windows.System.Launcher**](https://docs.microsoft.com/uwp/api/Windows.System.Launcher), чтобы запустить обработчик по умолчанию для файла, который приложение не может обработать самостоятельно.

## <a name="get-the-file-object"></a>Получение объекта файла

Сначала получите объект [**Windows.Storage.StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) для данного файла.

Если файл включен в пакет вашего приложения, можно использовать свойство [**Package.InstalledLocation**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.package.installedlocation) для получения объекта [**Windows.Storage.StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) и метод [**Windows.Storage.StorageFolder.GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync) для получения объекта [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile).

Если файл находится в известной папке, можно использовать свойства класса [**Windows.Storage.KnownFolders**](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders) для получения [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) и метод [**GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync) для получения объекта [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile).

## <a name="launch-the-file"></a>Запуск файла

Windows предоставляет несколько вариантов для запуска обработчика файла по умолчанию. Они описаны на диаграмме и в следующих разделах.

| Параметр | Метод | Описание |
|--------|--------|-------------|
| Запуск по умолчанию | [**LaunchFileAsync(IStorageFile)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync) | Запуск указанного файла с помощью обработчика по умолчанию. |
| Запуск через пункт меню "Открыть с помощью" | [**LaunchFileAsync(IStorageFile, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) | Запуск указанного файла, позволяющий пользователю выбрать обработчик в диалоговом окне «Открыть с помощью». |
| Запуск с помощью рекомендованного резервного приложения | [**LaunchFileAsync(IStorageFile, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) | Запуск указанного файла с помощью обработчика по умолчанию. Если в системе не установлен обработчик, следует порекомендовать пользователю приложение в Магазине. |
| Запуск с использованием заданного представления оставшегося пространства | [**LaunchFileAsync (IStorageFile, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) (только для Windows) | Запуск указанного файла с помощью обработчика по умолчанию. Укажите значение параметра, позволяющее остаться на экране после запуска, и запросите конкретный размер окна. [**LauncherOptions.DesiredRemainingView** ](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview) не поддерживается в семействе версий мобильных устройств. |

### <a name="default-launch"></a>Запуск по умолчанию

Чтобы запустить приложение по умолчанию, вызовите метод [**Windows.System.Launcher.LaunchFileAsync(IStorageFile)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync). В следующем примере используется метод [**Windows.Storage.StorageFolder.GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync) для запуска файла изображения test.png, который включен в пакет приложения.

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.png";
   
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);
   
   if (file != null)
   {
      // Launch the retrieved file
      var success = await Windows.System.Launcher.LaunchFileAsync(file);

      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()
   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.png"
   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)
   
   If file IsNot Nothing Then
      ' Launch the retrieved file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Launch the retrieved file
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Launch the retrieved file
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

### <a name="open-with-launch"></a>Запуск через пункт меню "Открыть с помощью"

Вызовите метод [**Windows.System.Launcher.LaunchFileAsync(IStorageFile, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) с заданным для параметра [**LauncherOptions.DisplayApplicationPicker**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.displayapplicationpicker) значением **true**, чтобы запустить приложение, которое пользователь выбирает в диалоговом окне **Открыть с помощью**.

Если пользователь хочет выбрать приложение для определенного файла, отличное от приложения по умолчанию, рекомендуется использовать диалоговое окно **Открыть с помощью**. Например, когда ваше приложение разрешает пользователю запускать файл изображения, по умолчанию наиболее вероятным обработчиком будет средство просмотра изображений. Иногда пользователю нужно не просто посмотреть изображение, но и отредактировать его. В таком случае используйте параметр **Открыть с помощью** наряду с альтернативной командой на панели приложения **AppBar** или в контекстном меню, чтобы пользователь мог вызвать диалоговое окно **Открыть с помощью** и выбрать приложение для редактирования.

![Диалоговое окно «Открыть с помощью» для запуска PNG-файла. Диалоговое окно содержит флажок, который определяет, будет ли выбор пользователя применяться ко всем или только к данному PNG-файлу. Диалоговое окно содержит четыре параметра приложения для запуска файла и ссылку "Дополнительные параметры".](images/checkboxopenwithdialog.png)

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
      string imageFile = @"images\test.png";
      
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the option to show the picker
      var options = new Windows.System.LauncherOptions();
      options.DisplayApplicationPicker = true;

      // Launch the retrieved file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()

   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.png"

   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)

   If file IsNot Nothing Then
      ' Set the option to show the picker
      Dim options = Windows.System.LauncherOptions()
      options.DisplayApplicationPicker = True

      ' Launch the retrieved file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the option to show the picker
        Windows::System::LauncherOptions launchOptions;
        launchOptions.DisplayApplicationPicker(true);

        // Launch the retrieved file
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the option to show the picker
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->DisplayApplicationPicker = true;

         // Launch the retrieved file
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

**Запустить с резервной рекомендуемые приложения**

В некоторых случаях у пользователя может быть не установлено приложение для обработки запускаемого файла. Тогда по умолчанию Windows предоставит пользователю ссылку для поиска подходящего приложения в Магазине. Если вы при этом хотите порекомендовать пользователю конкретное приложение, вы можете передать рекомендацию вместе с запускаемым файлом. Для этого вызовите метод [**Windows.System.Launcher.launchFileAsync(IStorageFile, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_), указав в качестве значения параметра [**LauncherOptions.PreferredApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname) имя семейства пакета приложения Магазина, которое вы рекомендуете. Затем задайте для параметра [**LauncherOptions.PreferredApplicationDisplayName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationdisplayname) имя этого приложения. Windows будет использовать эту информацию, чтобы заменить общий параметр (поиск приложения в Магазине) конкретным параметром (приобретение рекомендованного приложения в Магазине).

> [!NOTE]
> Необходимо задать оба этих варианта, чтобы рекомендовать приложения. Настройка одного параметра без другого приведет к ошибке.

![Диалоговое окно «Открыть с помощью» для запуска файла CONTOSO. Поскольку для формата CONTOSO на компьютере не установлен обработчик, диалоговое окно содержит параметр со значком Магазина и текстом, направляющим пользователя к правильному приложению в Магазине. Диалоговое окно также содержит ссылку "Дополнительные параметры".](images/howdoyouwanttoopen.png)

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.contoso";

   // Get the image file from the package's image directory
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the recommended app
      var options = new Windows.System.LauncherOptions();
      options.PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
      options.PreferredApplicationDisplayName = "Contoso File App";

      // Launch the retrieved file pass in the recommended app
      // in case the user has no apps installed to handle the file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()

   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.contoso"

   ' Get the image file from the package's image directory
   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)

   If file IsNot Nothing Then
      ' Set the recommended app
      Dim options = Windows.System.LauncherOptions()
      options.PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
      options.PreferredApplicationDisplayName = "Contoso File App";

      ' Launch the retrieved file pass in the recommended app
      ' in case the user has no apps installed to handle the file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the recommended app
        Windows::System::LauncherOptions launchOptions;
        launchOptions.PreferredApplicationPackageFamilyName(L"Contoso.FileApp_8wknc82po1e");
        launchOptions.PreferredApplicationDisplayName(L"Contoso File App");

        // Launch the retrieved file, and pass in the recommended app
        // in case the user has no apps installed to handle the file.
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.contoso"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the recommended app
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
         launchOptions->PreferredApplicationDisplayName = "Contoso File App";
         
         // Launch the retrieved file pass, and in the recommended app
         // in case the user has no apps installed to handle the file.
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

### <a name="launch-with-a-desired-remaining-view-windows-only"></a>Запуск с использованием требуемого представления оставшегося пространства (только для Windows)

Исходные приложения, вызывающие [**LaunchFileAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync), могут запрашивать разрешение остаться на экране после запуска файла. По умолчанию Windows пытается поровну поделить все доступное пространство между исходным приложением и конечным приложением, обрабатывающим файл. Исходные приложения могут использовать свойство [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview), чтобы сообщить операционной системе, что для окна приложения требуется больше или меньше доступного пространства. **DesiredRemainingView** также используется, чтобы сообщить системе, что исходному приложению не нужно оставаться на экране после запуска файла и что его пространство можно полностью занять конечным приложением. Это свойство указывает только предпочтительный размер окна для вызывающего приложения. Оно не задает условия для других приложений, которые могут находиться на экране в это же время.

> [!NOTE]
> Windows учитывает несколько факторов при определении исходного приложения конечного размера окна, например, из исходного приложения предпочтений, количество приложений на экране, ориентацию экрана и т. д. Задав [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview), вы не гарантируете конкретного поведения окон для исходного приложения.

**Семейства мобильных устройств:  **[**LauncherOptions.DesiredRemainingView** ](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview) не поддерживается в семействе версий мобильных устройств.

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.png";
   
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the desired remaining view
      var options = new Windows.System.LauncherOptions();
      options.DesiredRemainingView = Windows.UI.ViewManagement.ViewSizePreference.UseLess;

      // Launch the retrieved file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the desired remaining view.
        Windows::System::LauncherOptions launchOptions;
        launchOptions.DesiredRemainingView(Windows::UI::ViewManagement::ViewSizePreference::UseLess);

        // Launch the retrieved file.
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the desired remaining view.
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->DesiredRemainingView = Windows::UI::ViewManagement::ViewSizePreference::UseLess;

         // Launch the retrieved file.
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

## <a name="remarks"></a>Примечания

Ваше приложение не может выбрать запускаемое приложение. Пользователь сам определяет, какое приложение запустить. Пользователь может выбрать приложение универсальной платформы Windows (UWP) или классическое приложение для Windows.

При запуске файла ваше приложение должно быть приложением переднего плана, то есть быть видимым пользователю. Это требование обеспечивает контроль пользователя над ситуацией. Для соблюдения этого требования убедитесь, что все запуски файлов непосредственно связаны с пользовательским интерфейсом вашего приложения. Чтобы запустить файл, пользователь, скорее всего, должен выполнить какое-либо действие.

Нельзя запускать типы файлов, содержащие код или сценарий, если они автоматически выполняются операционной системой, например файлы EXE, MSI и JS. Это ограничение защищает пользователей от потенциально вредоносных файлов, которые могут внести изменения в операционную систему. Вы можете использовать этот метод для запуска файлов, содержащих сценарий, если они выполняются приложением, которое изолирует этот сценарий. К таким файлам, например, относятся файлы типа DOCX. Приложения, подобные Microsoft Word, не позволяют сценариям в DOCX-файлах изменять операционную систему.

Если вы попытаетесь запустить файл ограниченного типа, запуск завершится ошибкой, и будет выполнен обратный вызов для ошибки. Если ваше приложение обрабатывает разные типы файлов и вы считаете такую ошибку вероятной, рекомендуется предоставить пользователю резервный вариант действий. Например, можно позволить пользователю сохранить файл на рабочем столе и открыть его с рабочего стола.

## <a name="related-topics"></a>См. также

### <a name="tasks"></a>Задачи

* [Запуск приложения по умолчанию для URI](launch-default-app.md)
* [Обработка активации файла](handle-file-activation.md)

### <a name="guidelines"></a>Руководство

* [Рекомендации для типов файлов и URI](https://docs.microsoft.com/windows/uwp/files/index)

### <a name="reference"></a>Ссылка

* [**Windows.Storage.StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile)
* [**Windows.System.Launcher.LaunchFileAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchfileasync)

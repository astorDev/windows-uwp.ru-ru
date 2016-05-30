---
author: mcleblanc
title: Запуск приложения с помощью автозапуска
description: Автозапуск можно использовать для добавления приложения в список вариантов при подключении пользователем устройств к компьютеру. Это могут быть как устройства томов, такие как USB-устройства флэш-памяти, SD-карты или DVD-диски, так и прочие устройства, такие как камеры или проигрыватели мультимедиа.
ms.assetid: AD4439EA-00B0-4543-887F-2C1D47408EA7
---

# <span id="dev_launch_resume.auto-launching_with_autoplay"></span>Запуск приложения с помощью автозапуска


\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Автозапуск** можно использовать для добавления приложения в список вариантов при подключении пользователем устройств к компьютеру. Это могут быть как устройства томов, такие как USB-устройства флэш-памяти, SD-карты или DVD-диски, так и прочие устройства, такие как камеры или проигрыватели мультимедиа. **Автозапуск** также можно использовать, чтобы предложить свое приложение в качестве одного из открытых для общего доступа, при котором пользователи обмениваются файлами между двумя компьютерами с помощью близкого взаимодействия (касания).

> **Примечание**  Если вы являетесь изготовителем устройства и хотите назначить свое [приложение Магазина Windows для устройства](http://go.microsoft.com/fwlink/p/?LinkID=301381) в качестве обработчика **автозапуска** для вашего устройства, вы можете задать это приложение в метаданных устройства. Подробнее см. в разделе об [автозапуске приложений Магазина Windows для устройства](http://go.microsoft.com/fwlink/p/?LinkId=306684).

## Регистрация для автозапуска содержимого

Вы можете регистрировать приложения как варианты событий **автозапуска** содержимого. События содержимого **автозапуска** возникают, когда к компьютеру подключается устройство тома, например карта памяти камеры, флэш-накопитель или DVD-диск. В этом разделе показано, как определить приложение в качестве варианта **автозапуска** при подключении карты памяти камеры.

С помощью этого учебника вы создали приложение, которое отображает файлы изображений или копирует их в библиотеку изображений. Вы зарегистрировали приложение для события автозапуска содержимого **ShowPicturesOnArrival**.

Автозапуск также создает события содержимого для содержимого, которое совместно используется на компьютерах с помощью близкого взаимодействия (касания). Вы можете использовать действия и код, приведенные в этом разделе, для обработки файлов, совместно используемых на компьютерах при помощи близкого взаимодействия. В таблице ниже перечислены события автозапуска содержимого, доступные при обмене содержимым с помощью близкого взаимодействия.

| Действие         | Событие автозапуска содержимого  |
|----------------|-------------------------|
| Обмен музыкой  | PlayMusicFilesOnArrival |
| Обмен видео | PlayVideoFilesOnArrival |

 
Когда обмен файлами обеспечивается с помощью бесконтактного взаимодействия, свойство **Files** объекта **FileActivatedEventArgs** содержит ссылку на корневую папку, в которой находятся все файлы, открытые для общего доступа.

### Шаг 1. Создание нового проекта и добавление объявлений автозапуска

1.  Откройте Microsoft Visual Studio и выберите **Создать проект** в меню **Файл**. В разделе **Visual C#** под заголовком **Windows** выберите **Пустое приложение (универсальное приложение Windows)**. Назовите приложение **AutoPlayDisplayOrCopyImages** и нажмите кнопку **OK.**
2.  Откройте файл Package.appxmanifest и выберите вкладку **Возможности**. Выберите возможности **Съемные носители** и **Библиотека изображений**. Это обеспечит приложению доступ к съемным запоминающим устройствам для памяти камеры и к локальным изображениям.
3.  В файле манифеста выберите вкладку **Declarations** (Объявления). В раскрывающемся списке **Available Declarations** (Доступные объявления) выберите **AutoPlay Content** (Автозапуск содержимого) и нажмите **Add** (Добавить). Выберите новый элемент **AutoPlay Content** (Автозапуск содержимого), добавленный к списку **Supported Declarations** (Поддерживаемые объявления).
4.  Когда автозапуск порождает событие содержимого, объявление **Автозапуск содержимого** опознает ваше приложение как возможный вариант. Это событие основывается на содержимом устройства тома, такого как флэш-накопитель или DVD-диск. Автозапуск проверяет содержимое устройства тома и определяет, какое событие содержимого создать. Если в корне тома содержится папка с именем DCIM, AVCHD или PRIVATE\\ACHD, либо если пользователь включил параметр **Выберите, что требуется сделать с каждым из типов носителей** в окне "Автозапуск (Панель управления)" и в корне тома обнаружены изображения, автозапуск создает событие **ShowPicturesOnArrival**. В разделе **Действия при запуске** введите значения из приведенной ниже таблицы 1 для первого действия при запуске.
5.  В разделе **Действия при запуске** для элемента **Автозапуск содержимого** щелкните **Добавить**, чтобы добавить второе действие при запуске. Введите значения в приведенной ниже таблице 2 для второго действия при запуске.
6.  В раскрывающемся списке **Доступные объявления** выберите **Сопоставления типов файлов** и нажмите кнопку **Добавить**. В разделе "Свойства" нового объявления **Сопоставление типов файлов** выберите для поля **Отображаемое имя** значение **AutoPlay Copy or Show Images**, а для поля **Имя** — **image\_association1**. В разделе **Поддерживаемые типы файлов** нажмите кнопку **Добавить**. Для поля **Тип файла** установите значение **.jpg**. В разделе **Поддерживаемые типы файлов** установите для поля **Тип файла** нового сопоставления файлов значение **.png**. Для событий содержимого средство автозапуска отфильтровывает любые типы файлов, которые явно не связаны с приложением.
7.  Сохраните и закройте файл манифеста.


**Таблица 1**

| Параметр             | Значение                 |
|---------------------|-----------------------|
| Команда                | показать                  |
| Отображаемое имя действия | Показывать изображения         |
| Событие содержимого       | ShowPicturesOnArrival |

Параметр **Action Display Name** определяет строку, отображаемую автозапуском для приложения. Параметр **Verb** определяет значение, передаваемое приложению для выбранного варианта. Для события автозапуска можно указать несколько действий при запуске и использовать параметр **Verb**, чтобы определить, какой вариант пользователь выбрал для приложения. Выбранный пользователем вариант можно узнать, проверив свойство **verb** аргументов события запуска, переданных приложению. Для параметра **Verb** можно использовать любое значение, кроме значения **open**, которое зарезервировано.

**Таблица 2**  

| Параметр             | Значение                      |
|---------------------|----------------------------|
| Команда                | копировать                       |
| Отображаемое имя действия | Копировать изображения в библиотеку |
| Событие содержимого       | ShowPicturesOnArrival      |

### Шаг 2. Добавление пользовательского интерфейса XAML

Откройте файл MainPage.xaml и добавьте следующий код XAML в раздел &lt;Grid&gt; по умолчанию.

```xml
<TextBlock FontSize="18">File List</TextBlock>
<TextBlock x:Name="FilesBlock" HorizontalAlignment="Left" TextWrapping="Wrap" 
           VerticalAlignment="Top" Margin="0,20,0,0" Height="280" Width="240" />
<Canvas x:Name="FilesCanvas" HorizontalAlignment="Left" VerticalAlignment="Top" 
        Margin="260,20,0,0" Height="280" Width="100"/>
```

### Шаг 3. Добавление кода инициализации

На этом этапе код проверяет значение команды в свойстве **Verb**, которое является одним из аргументов запуска, передаваемых приложению во время события **OnFileActivated**. Затем код вызывает метод, связанный с вариантом, который выбрал пользователь. Для события памяти камеры автозапуск передает приложению корневую папку запоминающего устройства камеры. Вы можете получить эту папку из первого элемента свойства **Files**.

Откройте файл App.xaml.cs и добавьте следующий код в класс **App**.

```cs
protected override void OnFileActivated(FileActivatedEventArgs args)
{
    if (args.Verb == "show")
    {
        Frame rootFrame = (Frame)Window.Current.Content;
        MainPage page = (MainPage)rootFrame.Content;

        // Call DisplayImages with root folder from camera storage.
        page.DisplayImages((Windows.Storage.StorageFolder)args.Files[0]);
    }

    if (args.Verb == "copy")
    {
        Frame rootFrame = (Frame)Window.Current.Content;
        MainPage page = (MainPage)rootFrame.Content;

        // Call CopyImages with root folder from camera storage.
        page.CopyImages((Windows.Storage.StorageFolder)args.Files[0]);
    }

    base.OnFileActivated(args);
}
```

> **Примечание**  Методы `DisplayImages` и `CopyImages` будут добавлены на следующих этапах.

### Шаг 4. Добавление кода для отображения изображений

В файле MainPage.xaml.cs добавьте следующий код в класс **MainPage**.

```cs
async internal void DisplayImages(Windows.Storage.StorageFolder rootFolder)
{
    // Display images from first folder in root\DCIM.
    var dcimFolder = await rootFolder.GetFolderAsync("DCIM");
    var folderList = await dcimFolder.GetFoldersAsync();
    var cameraFolder = folderList[0];
    var fileList = await cameraFolder.GetFilesAsync();
    for (int i = 0; i < fileList.Count; i++)
    {
        var file = (Windows.Storage.StorageFile)fileList[i];
        WriteMessageText(file.Name + "\n");
        DisplayImage(file, i);
    }
}

async private void DisplayImage(Windows.Storage.IStorageItem file, int index)
{
    try
    {
        var sFile = (Windows.Storage.StorageFile)file;
        Windows.Storage.Streams.IRandomAccessStream imageStream =
            await sFile.OpenAsync(Windows.Storage.FileAccessMode.Read);
        Windows.UI.Xaml.Media.Imaging.BitmapImage imageBitmap =
            new Windows.UI.Xaml.Media.Imaging.BitmapImage();
        imageBitmap.SetSource(imageStream);
        var element = new Image();
        element.Source = imageBitmap;
        element.Height = 100;
        Thickness margin = new Thickness();
        margin.Top = index * 100;
        element.Margin = margin;
        FilesCanvas.Children.Add(element);
    }
    catch (Exception e)
    {
       WriteMessageText(e.Message + "\n");
    }
}

// Write a message to MessageBlock on the UI thread.
private Windows.UI.Core.CoreDispatcher messageDispatcher = Window.Current.CoreWindow.Dispatcher;

private async void WriteMessageText(string message, bool overwrite = false)
{
    await messageDispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
        () =>
        {
            if (overwrite)
                FilesBlock.Text = message;
            else
                FilesBlock.Text += message;
        });
}
```

### Шаг 5. Добавление кода для копирования изображений

В файле MainPage.xaml.cs добавьте следующий код в класс **MainPage**.

```cs
async internal void CopyImages(Windows.Storage.StorageFolder rootFolder)
{
    // Copy images from first folder in root\DCIM.
    var dcimFolder = await rootFolder.GetFolderAsync("DCIM");
    var folderList = await dcimFolder.GetFoldersAsync();
    var cameraFolder = folderList[0];
    var fileList = await cameraFolder.GetFilesAsync();

    try
    {
        var folderName = "Images " + DateTime.Now.ToString("yyyy-MM-dd HHmmss");
        Windows.Storage.StorageFolder imageFolder = await
            Windows.Storage.KnownFolders.PicturesLibrary.CreateFolderAsync(folderName);

        foreach (Windows.Storage.IStorageItem file in fileList)
        {
            CopyImage(file, imageFolder);
        }
    }
    catch (Exception e)
    {
        WriteMessageText("Failed to copy images.\n" + e.Message + "\n");
    }
}

async internal void CopyImage(Windows.Storage.IStorageItem file,
                              Windows.Storage.StorageFolder imageFolder)
{
    try
    {
        Windows.Storage.StorageFile sFile = (Windows.Storage.StorageFile)file;
        await sFile.CopyAsync(imageFolder, sFile.Name);
        WriteMessageText(sFile.Name + " copied.\n");
    }
    catch (Exception e)
    {
        WriteMessageText("Failed to copy file.\n" + e.Message + "\n");
    }
}
```

### Шаг 6. Сборка и запуск приложения

1.  Нажмите клавишу F5 для выполнения сборки и развертывания приложения (в режиме отладки).
2.  Чтобы запустить приложение, вставьте карту памяти камеры или другое запоминающее устройство из камеры в компьютер. Затем в списке вариантов автозапуска выберите один из вариантов событий содержимого, который вы указали в файле package.appxmanifest. Этот пример кода только показывает или копирует изображения в папке DCIM на карте памяти камеры. Если изображения хранятся в папке AVCHD или PRIVATE\\ACHD на карте памяти камеры, то потребуется изменить код соответствующим образом.
    **Примечание**  При отсутствии карты памяти камеры можно использовать устройство флэш-памяти, если в его корне имеется папка под названием **DCIM**, а в папке DCIM есть вложенная папка, содержащая изображения.

## Регистрация для автозапуска устройств


Вы можете зарегистрировать приложения в качестве вариантов для событий **автозапуска** устройств. События **AutoPlay** устройства возникают при подключении устройства к компьютеру.

В этом разделе показано, как определить приложение в качестве варианта **AutoPlay** при подключении камеры к компьютеру. Приложение регистрируется в качестве обработчика для события **WPD\\ImageSourceAutoPlay**. Это распространенное событие, которое вызывается системой переносного устройства Windows (WPD) при уведомлении камерами или другими устройствами обработки изображений о том, что они являются источниками изображений (ImageSource), использующими MTP. Подробнее см. в разделе о [переносных устройствах Windows](https://msdn.microsoft.com/library/windows/hardware/ff597729).

**Важно**  API [**Windows.Devices.Portable.StorageDevice**](https://msdn.microsoft.com/library/windows/apps/br225654) являются частью [семейства настольных устройств](https://msdn.microsoft.com/library/windows/apps/dn894631). Приложения могут использовать эти API только на устройствах Windows 10 из семейства настольных устройств, например компьютерах.

 

### Шаг 1. Создание нового проекта и добавление объявлений автозапуска

1.  Откройте Visual Studio и выберите пункт **Создать проект** в меню **Файл**. В разделе **Visual C#** под заголовком **Windows** выберите **Пустое приложение (универсальное приложение Windows)**. Назовите приложение **AutoPlayDevice\_Camera** и щелкните **OK.**
2.  Откройте файл Package.appxmanifest и выберите вкладку **Характеристики**. Выберите возможность **Съемные носители**. Это дает приложению доступ к данным на камере как на томе съемного носителя.
3.  В файле манифеста выберите вкладку **Declarations** (Объявления). В раскрывающемся списке **Available Declarations** (Доступные объявления) выберите **AutoPlay Device** (Автозапуск устройства) и нажмите кнопку **Add** (Добавить). Выберите новый элемент **AutoPlay Device** (Автозапуск устройства), добавленный к списку **Supported Declarations** (Поддерживаемые объявления).
4.  Объявление **Автозапуск устройства** идентифицирует приложение как вариант, когда автозапуск генерирует событие устройства для известных событий. В разделе **Действия при запуске** введите значения в приведенной ниже таблице 1 для первого действия при запуске.
5.  В раскрывающемся списке **Доступные объявления** выберите **Сопоставления типов файлов** и нажмите кнопку **Добавить**. В разделе "Свойства" нового объявления **Сопоставление типов файлов** выберите для поля **Отображаемое имя** значение **Show Images from Camera**, а для поля **Имя** — **camera\_association1**. В разделе **Поддерживаемые типы файлов** щелкните **Добавить** (если необходимо). Для поля **Тип файла** установите значение **.jpg**. В разделе **Поддерживаемые типы файлов** еще раз щелкните **Добавить**. Установите для поля **Тип файла** нового сопоставления файлов значение **.png**. Для событий содержимого средство автозапуска отфильтровывает любые типы файлов, которые явно не связаны с приложением.
6.  Сохраните и закройте файл манифеста.

| Параметр             | Значение            |
|---------------------|------------------|
| Команда                | показать             |
| Отображаемое имя действия | Показывать изображения    |
| Событие содержимого       | WPD\\ImageSource |

Параметр **Отображаемое имя действия** определяет строку, отображаемую автозапуском для приложения. Параметр **Verb** определяет значение, передаваемое приложению для выбранного варианта. Для события автозапуска можно указать несколько действий при запуске и использовать параметр **Verb**, чтобы определить, какой вариант пользователь выбрал для приложения. Выбранный пользователем вариант можно узнать, проверив свойство **verb** аргументов события запуска, переданных приложению. Для параметра **Verb** можно использовать любое значение, кроме значения **open**, которое зарезервировано. Пример использования нескольких команд в одном приложении см. в разделе [Регистрация для автозапуска содержимого](#autoplaycontent).

### Шаг 2. Добавление ссылки на сборку для настольных расширений

API, необходимые для получения доступа к хранилищу на переносном устройстве Windows, [**Windows.Devices.Portable.StorageDevice**](https://msdn.microsoft.com/library/windows/apps/br225654), являются частью [семейства настольных компьютеров](https://msdn.microsoft.com/library/windows/apps/dn894631). Это означает, что для использования этих API требуется специальная сборка и что эти вызовы будут работать только на устройстве из семейства настольных устройств (например, компьютере).

1.  В **Обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку...**
2.  Разверните папку **Универсальные приложения для Windows** и выберите **Расширения**.
3.  Выберите **Windows Desktop Extensions for the UWP** (Расширения Windows Desktop для UWP) и нажмите **ОК**.

### Шаг 3. Добавление пользовательского интерфейса XAML

Откройте файл MainPage.xaml и добавьте следующий код XAML в раздел &lt;Grid&gt; по умолчанию.

```xml
<StackPanel Orientation="Vertical" Margin="10,0,-10,0">
    <TextBlock FontSize="24">Device Information</TextBlock>
    <StackPanel Orientation="Horizontal">
        <TextBlock x:Name="DeviceInfoTextBlock" FontSize="18" Height="400" Width="400" VerticalAlignment="Top" />
        <ListView x:Name="ImagesList" HorizontalAlignment="Left" Height="400" VerticalAlignment="Top" Width="400">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Vertical">
                        <Image Source="{Binding Path=Source}" />
                        <TextBlock Text="{Binding Path=Name}" />
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
            <ListView.ItemsPanel>
                <ItemsPanelTemplate>
                    <WrapGrid Orientation="Horizontal" ItemHeight="100" ItemWidth="120"></WrapGrid>
                </ItemsPanelTemplate>
            </ListView.ItemsPanel>
        </ListView>
    </StackPanel>
</StackPanel>
```

### Шаг 4. Добавление кода активации

Код на этом этапе ссылается на камеру как на [**StorageDevice**](https://msdn.microsoft.com/library/windows/apps/br225654) путем передачи идентификатора сведений устройства методу [**FromId**](https://msdn.microsoft.com/library/windows/apps/br225655). Идентификатор сведений устройства можно получить путем отправки аргументов события как [**DeviceActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224710) и последующего получения значения из свойства [**DeviceInformationId**](https://msdn.microsoft.com/library/windows/apps/br224711).

Откройте файл App.xaml.cs и добавьте следующий код в класс **App**.

```cs
protected override void OnActivated(IActivatedEventArgs args)
{
   if (args.Kind == ActivationKind.Device)
   {
      Frame rootFrame = null;
      // Ensure that the current page exists and is activated
      if (Window.Current.Content == null)
      {
         rootFrame = new Frame();
         rootFrame.Navigate(typeof(MainPage));
         Window.Current.Content = rootFrame;
      }
      else
      {
         rootFrame = Window.Current.Content as Frame;
      }
      Window.Current.Activate();

      // Make sure the necessary APIs are present on the device
      bool storageDeviceAPIPresent =
      Windows.Foundation.Metadata.ApiInformation.IsTypePresent("Windows.Devices.Portable.StorageDevice");

      if (storageDeviceAPIPresent)
      {
         // Reference the current page as type MainPage
         var mPage = rootFrame.Content as MainPage;

         // Cast the activated event args as DeviceActivatedEventArgs and show images
         var deviceArgs = args as DeviceActivatedEventArgs;
         if (deviceArgs != null)
         {
            mPage.ShowImages(Windows.Devices.Portable.StorageDevice.FromId(deviceArgs.DeviceInformationId));
         }
      }
      else
      {
         // Handle case where APIs are not present (when the device is not part of the desktop device family)
      }

   }

   base.OnActivated(args);
}
```

> **Примечание**  Метод `ShowImages` будет добавлен на следующем этапе.

### Шаг 5. Добавление кода для отображения сведений об устройстве

Вы можете получить информацию о камере из свойств класса [**StorageDevice**](https://msdn.microsoft.com/library/windows/apps/br225654). Код на этом этапе выводит имя устройства и другую информацию для пользователя при запуске приложения. Затем код вызывает методы GetImageList и GetThumbnail, которые будут добавлены на следующем этапе, для отображения эскизов изображений, сохраненных в камере.

В файле MainPage.xaml.cs добавьте следующий код в класс **MainPage**.

```cs
private Windows.Storage.StorageFolder rootFolder;

internal async void ShowImages(Windows.Storage.StorageFolder folder)
{
    DeviceInfoTextBlock.Text = "Display Name = " + folder.DisplayName + "\n";
    DeviceInfoTextBlock.Text += "Display Type =  " + folder.DisplayType + "\n";
    DeviceInfoTextBlock.Text += "FolderRelativeId = " + folder.FolderRelativeId + "\n";

    // Reference first folder of the device as the root
    rootFolder = (await folder.GetFoldersAsync())[0];
    var imageList = await GetImageList(rootFolder);

    foreach (Windows.Storage.StorageFile img in imageList)
    {
        ImagesList.Items.Add(await GetThumbnail(img));
    }
}
```

> **Примечание**  Методы `GetImageList` и `GetThumbnail` будут добавлены на следующем этапе.

 

### Шаг 6. Добавление кода для отображения изображений

Код на этом этапе отображает эскизы изображений, сохраненных в камере. Код выполняет асинхронные вызовы камеры, чтобы получить эскиз изображения. Однако следующий асинхронный вызов не выполняется до завершения предыдущего. Это гарантирует, что единовременно к камере осуществляется только один запрос.

В файле MainPage.xaml.cs добавьте следующий код в класс **MainPage**.

```cs
async private System.Threading.Tasks.Task<List<Windows.Storage.StorageFile>> GetImageList(Windows.Storage.StorageFolder folder) 
{
    var result = await folder.GetFilesAsync();
    var subFolders = await folder.GetFoldersAsync();
    foreach (Windows.Storage.StorageFolder f in subFolders)
        result = result.Union(await GetImageList(f)).ToList();

    return (from f in result orderby f.Name select f).ToList();
}

async private System.Threading.Tasks.Task<Image> GetThumbnail(Windows.Storage.StorageFile img) 
{
    // Get the thumbnail to display
    var thumbnail = await img.GetThumbnailAsync(Windows.Storage.FileProperties.ThumbnailMode.SingleItem,
                                                100,
                                                Windows.Storage.FileProperties.ThumbnailOptions.UseCurrentScale);

    // Create a XAML Image object bind to on the display page
    var result = new Image();
    result.Height = thumbnail.OriginalHeight;
    result.Width = thumbnail.OriginalWidth;
    result.Name = img.Name;
    var imageBitmap = new Windows.UI.Xaml.Media.Imaging.BitmapImage();
    imageBitmap.SetSource(thumbnail);
    result.Source = imageBitmap;

    return result;
}
```

### Шаг 7. Сборка и запуск приложения

1.  Нажмите клавишу F5 для выполнения сборки и развертывания приложения (в режиме отладки).
2.  Для запуска приложения подключите камеру к компьютеру. Затем выберите приложение из списка вариантов автозапуска.
    **Примечание**  Не все камеры передают сведения для события **WPD\\ImageSource** автозапуска устройства.

     

## Настройка съемного носителя


Устройство тома, например карту памяти или флэш-накопитель, можно определить как устройство **AutoPlay**, когда устройство тома подключается к компьютеру. Это особенно полезно, когда требуется назначить **автозапуск** для конкретного приложения, чтобы представлять пользователю устройство тома.

В этом разделе показано, как определить устройство тома в качестве устройства **автозапуска**.

Чтобы определить устройство тома в качестве устройства **AutoPlay**, добавьте файл autorun.inf в корневой диск устройства. В файле autorun.inf добавьте ключ **CustomEvent** в раздел **AutoRun**. Когда устройство тома подключится к компьютеру, функция **AutoPlay** найдет файл autorun.inf и будет рассматривать том как устройство. **AutoPlay** создаст событие **AutoPlay** с помощью имени, которое вы ввели для ключа **CustomEvent**. Затем можно создать приложение и зарегистрировать его в качестве обработчика для события **AutoPlay**. При подключении устройства к компьютеру **AutoPlay** отобразит ваше приложение как обработчик для устройства тома. Подробнее о файлах autorun.inf см. в разделе о [записях autorun.inf](https://msdn.microsoft.com/library/windows/desktop/cc144200).

### Шаг 1. Создание файла autorun.inf

В корневой диск устройства тома добавьте файл с именем autorun.inf. Откройте файл autorun.inf и добавьте в него следующий текст.

``` syntax
[AutoRun]
CustomEvent=AutoPlayCustomEventQuickstart
```

### Шаг 2. Создание нового проекта и добавление объявлений автозапуска

1.  Откройте Visual Studio и выберите пункт **Создать проект** в меню **Файл**. В разделе **Visual C#** под заголовком **Windows** выберите **Пустое приложение (универсальное приложение Windows)**. Назовите приложение **AutoPlayCustomEvent** и нажмите **OK.**
2.  Откройте файл Package.appxmanifest и выберите вкладку **Характеристики**. Выберите возможность **Съемные носители**. Это дает приложению доступ к файлам и папкам на съемных устройствах хранения.
3.  В файле манифеста выберите вкладку **Declarations** (Объявления). В раскрывающемся списке **Available Declarations** (Доступные объявления) выберите **AutoPlay Content** (Автозапуск содержимого) и нажмите **Add** (Добавить). Выберите новый элемент **Автозапуск содержимого**, добавленный к списку **Поддерживаемые объявления**.

    **Примечание**  Кроме того, вы можете добавить объявление **Автозапуск устройства** для вашего пользовательского события автозапуска.
    
4.  В разделе **Действия при запуске** объявления событий **Автозапуск содержимого** для первого действия запуска введите значения в приведенной ниже таблице.
5.  В раскрывающемся списке **Доступные объявления** выберите **Сопоставления типов файлов** и нажмите кнопку **Добавить**. В разделе "Свойства" нового объявления **Сопоставление типов файлов** выберите для поля **Отображаемое имя** значение **Show .ms Files**, а для поля **Имя** — **ms\_association**. В разделе **Поддерживаемые типы файлов** нажмите кнопку **Добавить**. Для поля **Тип файла** установите значение **.ms**. Для событий автозапуска содержимого средство автозапуска отфильтровывает любые типы файлов, которые явно не связаны с приложением.
6.  Сохраните и закройте файл манифеста.

| Параметр             | Значение                         |
|---------------------|-------------------------------|
| Команда                | показать                          |
| Отображаемое имя действия | Показать файлы                    |
| Событие содержимого       | AutoPlayCustomEventQuickstart |

Значение **Событие содержимого** представляет собой текст, который вы указали для ключа **CustomEvent** в файле autorun.inf. Параметр **Отображаемое имя действия** определяет строку, отображаемую автозапуском для приложения. Параметр **Verb** определяет значение, передаваемое приложению для выбранного варианта. Для события автозапуска можно указать несколько действий при запуске и использовать параметр **Verb**, чтобы определить, какой вариант пользователь выбрал для приложения. Выбранный пользователем вариант можно узнать, проверив свойство **verb** аргументов события запуска, переданных приложению. Для параметра **Verb** можно использовать любое значение, кроме значения **open**, которое зарезервировано.

### Шаг 3. Добавление пользовательского интерфейса XAML

Откройте файл MainPage.xaml и добавьте следующий код XAML в раздел &lt;Grid&gt; по умолчанию.

```xml
<StackPanel Orientation="Vertical">
    <TextBlock FontSize="28" Margin="10,0,800,0">Files</TextBlock>
    <TextBlock x:Name="FilesBlock" FontSize="22" Height="600" Margin="10,0,800,0" />
</StackPanel>
```

### Шаг 4. Добавление кода активации

Код на этом этапе вызывает метод для отображения папок в корневом диске устройства тома. Для событий автозапуска содержимого средство автозапуска обрабатывает корневую папку запоминающего устройства в аргументах запуска, передаваемых в приложение во время события **OnFileActivated**. Вы можете получить эту папку из первого элемента свойства **Files**.

Откройте файл App.xaml.cs и добавьте следующий код в класс **App**.

```cs
protected override void OnFileActivated(FileActivatedEventArgs args)
{
    var rootFrame = Window.Current.Content as Frame;
    var page = rootFrame.Content as MainPage;

    // Call ShowFolders with root folder from device storage.
    page.DisplayFiles(args.Files[0] as Windows.Storage.StorageFolder);

    base.OnFileActivated(args);
}
```

> **Примечание**  Метод `DisplayFiles` будет добавлен на следующем этапе.

 

### Шаг 5. Добавление кода для отображения папок

В файле MainPage.xaml.cs добавьте следующий код в класс **MainPage**.

```cs
internal async void DisplayFiles(Windows.Storage.StorageFolder folder)
{
    foreach (Windows.Storage.StorageFile f in await ReadFiles(folder, ".ms"))
    {
        FilesBlock.Text += "  " + f.Name + "\n";
    }
}

internal async System.Threading.Tasks.Task<IReadOnlyList<Windows.Storage.StorageFile>> 
    ReadFiles(Windows.Storage.StorageFolder folder, string fileExtension)
{
    var options = new Windows.Storage.Search.QueryOptions();
    options.FileTypeFilter.Add(fileExtension);
    var query = folder.CreateFileQueryWithOptions(options);
    var files = await query.GetFilesAsync();

    return files;
}
```

### Шаг 6. Сборка и запуск приложения

1.  Нажмите клавишу F5 для выполнения сборки и развертывания приложения (в режиме отладки).
2.  Для запуска приложения вставьте карту памяти или другое запоминающее устройство в компьютер. Затем выберите приложение из списка вариантов обработчика автозапуска.

## Ссылка события автозапуска


Система **автозапуска** позволяет зарегистрировать приложение для различных событий, связанных с подключением устройств и носителей информации (дисков). Чтобы зарегистрировать приложение для событий **автозапуска** содержимого, необходимо включить возможность **Съемные носители** в манифесте пакета. В следующей таблице представлены события, для которых вы можете зарегистрировать приложение, а также ситуации, в которых они создаются.

| Сценарий                                                           | Событие   | Описание   |
|--------------------------------------------------------------------|---------|---------------|
| Использование фотографий на камере                                           | **WPD\ImageSource**                | Создается для камер, которые определяются как переносные устройства Windows и обладают возможностью ImageSource.                                                                                                                                                                                                                                                                  |
| Использование музыки на аудиопроигрывателе                                     | **WPD\AudioSource**                | Создается для проигрывателей мультимедиа, которые определяются как переносные устройства Windows и обладают возможностью AudioSource.                                                                                                                                                                                                                                                            |
| Использование видеозаписей на видеокамере                                     | **WPD\VideoSource**                | Создается для видеокамер, которые определяются как переносные устройства Windows и обладают возможностью VideoSource.                                                                                                                                                                                                                                                            |
| Доступ к подключенному устройству флэш-памяти или внешнему жесткому диску              | **StorageOnArrival**               | Создается, когда к компьютеру подключается диск или том.   Если диск или том содержит папку DCIM, AVCHD или PRIVATE\ACHD в корне диска, то вместо него создается событие **ShowPicturesOnArrival**.                                                                                                                                                             |
| Использование фотографий с запоминающих устройств (устаревших типов)                            | **ShowPicturesOnArrival**          | Создается, когда диск или том содержит папку DCIM, AVCHD или PRIVATE\ACHD в корне диска. Если пользователь включил параметр **Выберите, что требуется сделать с каждым из типов носителей** в окне "Автозапуск (Панель управления)", то программа автозапуска проверяет подключенный к компьютеру том, чтобы определить тип содержимого на диске. При обнаружении изображений создается событие **ShowPicturesOnArrival**. |
| Получение фотографий с помощью бесконтактного общего доступа (коснуться и отправить)             | **ShowPicturesOnArrival**          | Когда пользователи отправляют содержимое с помощью бесконтактного взаимодействия (коснуться и отправить), программа автозапуска проверяет совместно используемые файлы, чтобы определить тип содержимого. При обнаружении изображений создается событие **ShowPicturesOnArrival**.                                                                                                                                                                         |
| Использование музыки с запоминающих устройств (устаревших типов)                             | **PlayMusicFilesOnArrival**        | Если пользователь включил параметр **Выберите, что требуется сделать с каждым из типов носителей** в окне "Автозапуск (Панель управления)", то программа автозапуска проверяет подключенный к компьютеру том, чтобы определить тип содержимого на диске.  При обнаружении музыкальных файлов создается событие **PlayMusicFilesOnArrival**.                                                                                                   |
| Получение музыки с помощью бесконтактного общего доступа (коснуться и отправить)              | **PlayMusicFilesOnArrival**        | Когда пользователи отправляют содержимое с помощью бесконтактного взаимодействия (коснуться и отправить), программа автозапуска проверяет совместно используемые файлы, чтобы определить тип содержимого. При обнаружении музыкальных файлов создается событие **PlayMusicFilesOnArrival**.                                                                                                                                                                    |
| Использование видеозаписей с запоминающих устройств (устаревших типов)                            | **PlayVideoFilesOnArrival**        | Если пользователь включил параметр **Выберите, что требуется сделать с каждым из типов носителей** в окне "Автозапуск (Панель управления)", то программа автозапуска проверяет подключенный к компьютеру том, чтобы определить тип содержимого на диске. При обнаружении видеофайлов создается событие **PlayVideoFilesOnArrival**.                                                                                                   |
| Получение видеозаписей с помощью бесконтактного общего доступа (коснуться и отправить)             | **PlayVideoFilesOnArrival**        | Когда пользователи отправляют содержимое с помощью бесконтактного взаимодействия (коснуться и отправить), программа автозапуска проверяет совместно используемые файлы, чтобы определить тип содержимого. В случае обнаружения видеофайлов создается событие **PlayVideoFilesOnArrival**.                                                                                                                                                                    |
| Обработка смешанных наборов файлов, находящихся на подключенном устройстве               | **MixedContentOnArrival**          | Если пользователь включил параметр **Выберите, что требуется сделать с каждым из типов носителей** в окне "Автозапуск (Панель управления)", то программа автозапуска проверяет подключенный к компьютеру том, чтобы определить тип содержимого на диске. Если не обнаружено конкретного типа содержимого (например, изображений), то создается событие **MixedContentOnArrival**.                                                                    |
| Обработка смешанных наборов файлов, получаемых с помощью бесконтактного общего доступа (коснуться и отправить) | **MixedContentOnArrival**          | Когда пользователи отправляют содержимое с помощью бесконтактного взаимодействия (коснуться и отправить), программа автозапуска проверяет совместно используемые файлы, чтобы определить тип содержимого. Если не обнаружено конкретного типа содержимого (например, изображений), то создается событие **MixedContentOnArrival**.                                                                                                                                  |
| Обработка видеозаписей с оптических носителей                                    | **PlayDVDMovieOnArrival**          |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **PlayBluRayOnArrival**            |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **PlayVideoCDMovieOnArrival**      |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **PlaySuperVideoCDMovieOnArrival** |                                                                                                                                                                                                                                                                                                                                                                           |
| Обработка музыки с оптических носителей                                    | **PlayCDAudioOnArrival**           |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **PlayDVDAudioOnArrival**          |                                                                                                                                                                                                                                                                                                                                                                           |
| Воспроизведение улучшенных дисков                                                | **PlayEnhancedCDOnArrival**        |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **PlayEnhancedDVDOnArrival**       |                                                                                                                                                                                                                                                                                                                                                                           |
| Обработка записываемых оптических дисков                                     | **HandleCDBurningOnArrival**       |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **HandleDVDBurningOnArrival**      |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    |                                    |                                                                                                                                                                                                                                                                                                                                                                           |
|                                                                    | **HandleBDBurningOnArrival**       |                                                                                                                                                                                                                                                                                                                                                                           |
| Обработка любых других подключенных устройств или накопителей                       | **UnknownContentOnArrival**        | Создается во всех прочих случаях, если обнаружено содержимое, которое не соответствует ни одному из событий автозапуска содержимого. Использовать это событие не рекомендуется. Вам следует регистрировать ваше приложение только для конкретных событий автозапуска, которые оно может обработать.                                                                                                                               |

Вы можете настроить автозапуск таким образом, чтобы пользовательское событие автозапуска содержимого создавалось с помощью записи **CustomEvent** в файле autorun.inf для тома. Подробнее см. [Записи Autorun.inf](https://msdn.microsoft.com/library/windows/desktop/cc144200).

Вы можете зарегистрировать приложение в качестве обработчика событий автозапуска содержимого или устройства автозапуска, добавив расширение для файла package.appxmanifest приложения. При использовании Visual Studio можно добавить объявление **Автозапуск содержимого** или **Автозапуск устройства** на вкладке **Объявления**. Если вы непосредственно редактируете файл package.appxmanifest для приложения, добавьте элемент [**Extension**](https://msdn.microsoft.com/library/windows/apps/br211400) в манифест пакета, который задает **windows.autoPlayContent** или **windows.autoPlayDevice** в качестве **Категории**. Например, следующая запись в манифесте пакета добавляет расширение **Автозапуск содержимого**, чтобы зарегистрировать приложение в качестве обработчика события **ShowPicturesOnArrival**.

```xml
  <Applications>
    <Application Id="AutoPlayHandlerSample.App">
      <Extensions>
        <Extension Category="windows.autoPlayContent">
          <AutoPlayContent>
            <LaunchAction Verb="show" ActionDisplayName="Show Pictures" 
                          ContentEvent="ShowPicturesOnArrival" />
          </AutoPlayContent>
        </Extension>
      </Extensions>
    </Application>
  </Applications>
```

 

 





<!--HONumber=May16_HO2-->



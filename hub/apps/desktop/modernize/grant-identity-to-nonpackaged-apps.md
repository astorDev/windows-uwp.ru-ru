---
Description: Сведения о предоставлении идентификатора для неупакованных классических приложений, чтобы в них можно было использовать современные функции Windows 10.
title: Предоставление идентификаторов для неупакованных классических приложений
ms.date: 02/28/2020
ms.topic: article
keywords: windows 10, классическое приложение, пакет, идентификатор, MSIX, Win32
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 6b77cc7b2f39a987df4c832f7a8daeb7e2722def
ms.sourcegitcommit: f2f61a43f5bc24b829e8db679ffaca3e663c00e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80588706"
---
# <a name="grant-identity-to-non-packaged-desktop-apps"></a>Предоставление идентификаторов для неупакованных классических приложений

<!--
> [!NOTE]
> The features described in this article require Windows 10 Insider Preview Build 10.0.19000.0 or a later release.
-->

При использовании многих возможностей расширяемости Windows 10, в том числе для фоновых задач, уведомлений, живых плиток и получателей данных для классических приложений, не относящихся к UWP, необходим [идентификатор пакета](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity). В этих сценариях для ОС необходим идентификатор, чтобы определить вызывающий объект соответствующего API.

В выпусках ОС, предшествующих Windows 10 Insider Preview сборки 10.0.19000.0, единственный способ предоставления идентификатора для классических приложений — [создание подписанного пакета MSIX](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root). Для этих приложений идентификатор указывается в манифесте пакета, а регистрацию идентификатора выполняет конвейер развертывания MSIX на основе информации в манифесте. Все содержимое, на которое ссылается манифест пакета, содержится в пакете MSIX.

Начиная со сборки 10.0.19000.0 Windows 10 Insider Preview вы можете предоставлять идентификаторы в пакетах для классических приложений, которые не упакованы в пакет MSIX, создав и зарегистрировав *разреженный пакет* с помощью приложения. Эта поддержка позволяет классическим приложениям, для которых еще нельзя внедрить упаковку MSIX для развертывания, использовать возможности расширяемости в Windows 10, для которых требуется идентификатор пакета. Дополнительные сведения см. в [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/10/29/identity-registration-and-activation-of-non-packaged-win32-apps/#HBMFEM843XORqOWx.97).

Чтобы создать и зарегистрировать разреженный пакет и предоставить в нем идентификатор для классического приложения, выполните действия ниже.

1. [Создайте манифест пакета для разреженного пакета](#create-a-package-manifest-for-the-sparse-package).
2. [Создайте и подпишите разреженный пакет](#build-and-sign-the-sparse-package).
3. [Добавьте метаданные идентификатора пакета в манифест классического приложения](#add-the-package-identity-metadata-to-your-desktop-application-manifest).
4. [Зарегистрируйте разреженный пакет во время выполнения](#register-your-sparse-package-at-run-time).

## <a name="important-concepts"></a>Важные понятия

Следующие компоненты позволяют неупакованным классическим приложениям получать идентификатор в пакете.

### <a name="sparse-packages"></a>Разреженные пакеты

*Разреженный пакет* содержит манифест в пакете, но не имеет других двоичных файлов приложения и содержимого. Манифест разреженного пакета может ссылаться на файлы за пределами пакета в предварительно определенном внешнем расположении. Это позволяет приложениям, для которых еще нельзя внедрить упаковку MSIX всего приложения, получить идентификатор пакета, необходимый для использования некоторых возможностей расширяемости в Windows 10.

> [!NOTE]
> Классическое приложение, использующее разреженный пакет, не получает некоторые преимущества полного развертывания с помощью пакета MSIX. К таким преимуществам относятся защита от подделки, установка в заблокированном расположении и полное управление ОС во время развертывания, выполнения и удаления.

### <a name="package-external-location"></a>Внешнее расположение пакета

Для поддержки разреженных пакетов схема манифеста теперь использует необязательный элемент **\<AllowExternalContent\>** в элементе [ **\<Properties\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-properties). Это позволяет манифесту пакета ссылаться на содержимое за пределами пакета в определенном расположении на диске.

Например, если у вас есть неупакованное классическое приложение, устанавливающее исполняемый файл и другое содержимое в папке C:\Program Files\MyDesktopApp\,, можно создать разреженный пакет, который размещает элемент **\<AllowExternalContent\>** в манифесте. Во время установки приложения или при первом запуске приложений можно установить разреженный пакет и объявить C:\Program Files\MyDesktopApp\ в качестве внешнего расположения, которое будет использовать приложение.

## <a name="create-a-package-manifest-for-the-sparse-package"></a>Создание манифеста пакета для разреженного пакета

Перед созданием разреженного пакета необходимо сначала создать [манифест пакета](https://docs.microsoft.com/uwp/schemas/appxpackage/appx-package-manifest) (файл с именем AppxManifest.xml), объявляющий метаданные идентификатора в пакете для классического приложения и другие необходимые сведения. Самый простой способ создать манифест пакета для разреженного пакета — использовать приведенный ниже пример и настроить его для приложения в соответствии со [справочником по схеме](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root).

Убедитесь, что манифест пакета содержит следующие элементы:

* Элемент [ **\<Identity\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity), описывающий атрибуты идентификатора для классического приложения.
* Элемент **\<AllowExternalContent\>** в элементе [ **\<Properties\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-properties). Этому элементу следует присвоить значение `true`, что позволяет манифесту пакета ссылаться на содержимое вне пакета в определенном расположении на диске. На следующем шаге вы укажете путь к внешнему расположению при регистрации разреженного пакета из кода, который выполняется в установщике или приложении. Любое содержимое, на которое вы ссылаетесь в манифесте и которое не находится в самом пакете, должно быть установлено во внешнем расположении.
* Для атрибута **MinVersion** элемента [ **\<TargetDeviceFamily\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) следует задать `10.0.19000.0` или более позднюю версию.
* Атрибуты **TrustLevel=mediumIL** и **RuntimeBehavior=Win32App** элемента [ **\<Application\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) объявляют, что классическое приложение, связанное с разреженным пакетом, будет выполняться аналогично стандартному неупакованному классическому приложению без виртуализации реестра и файловой системы, а также других изменений во время выполнения.

В следующем примере показано полное содержимое манифеста разреженного пакета (AppxManifest.xml). Этот манифест содержит расширение `windows.sharetarget`, для которого требуется идентификатор пакета.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Package 
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap2="http://schemas.microsoft.com/appx/manifest/uap/windows10/2"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  xmlns:uap10="http://schemas.microsoft.com/appx/manifest/uap/windows10/10"
  IgnorableNamespaces="uap uap2 uap3 rescap desktop uap10">
  <Identity Name="ContosoPhotoStore" ProcessorArchitecture="x64" Publisher="CN=Contoso" Version="1.0.0.0" />
  <Properties>
    <DisplayName>ContosoPhotoStore</DisplayName>
    <PublisherDisplayName>Contoso</PublisherDisplayName>
    <Logo>Assets\storelogo.png</Logo>
    <uap10:AllowExternalContent>true</uap10:AllowExternalContent>
  </Properties>
  <Resources>
    <Resource Language="en-us" />
  </Resources>
  <Dependencies>
    <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.19000.0" MaxVersionTested="10.0.19000.0" />
  </Dependencies>
  <Capabilities>
    <rescap:Capability Name="runFullTrust" />
    <rescap:Capability Name="unvirtualizedResources"/>
  </Capabilities>
  <Applications>
    <Application Id="ContosoPhotoStore" Executable="ContosoPhotoStore.exe" uap10:TrustLevel="mediumIL" uap10:RuntimeBehavior="win32App"> 
      <uap:VisualElements AppListEntry="none" DisplayName="Contoso PhotoStore" Description="Demonstrate photo app" BackgroundColor="transparent" Square150x150Logo="Assets\Square150x150Logo.png" Square44x44Logo="Assets\Square44x44Logo.png">
        <uap:DefaultTile Wide310x150Logo="Assets\Wide310x150Logo.png" Square310x310Logo="Assets\LargeTile.png" Square71x71Logo="Assets\SmallTile.png"></uap:DefaultTile>
        <uap:SplashScreen Image="Assets\SplashScreen.png" />
      </uap:VisualElements>
      <Extensions>
        <uap:Extension Category="windows.shareTarget">
          <uap:ShareTarget Description="Send to ContosoPhotoStore">
            <uap:SupportedFileTypes>
              <uap:FileType>.jpg</uap:FileType>
              <uap:FileType>.png</uap:FileType>
              <uap:FileType>.gif</uap:FileType>
            </uap:SupportedFileTypes>
            <uap:DataFormat>StorageItems</uap:DataFormat>
            <uap:DataFormat>Bitmap</uap:DataFormat>
          </uap:ShareTarget>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

## <a name="build-and-sign-the-sparse-package"></a>Сборка и подписывание разреженного пакета

После создания манифеста пакета создайте разреженный пакет с помощью [средства MakeAppx.exe](https://docs.microsoft.com/windows/msix/package/create-app-package-with-makeappx-tool), доступного в Windows SDK. Так как разреженный пакет не содержит файлы, на которые ссылается манифест, необходимо указать параметр `/nv`, который пропускает семантическую проверку для пакета.

В следующем примере показано, как создать разреженный пакет в командной строке.  

```Console
MakeAppx.exe pack /d <path to directory that contains manifest> /p <output path>\MyPackage.msix /nv
```

Чтобы установить разреженный пакет на конечном компьютере, необходимо подписать его с использованием сертификата, который является доверенным на конечном компьютере. Можно создать самозаверяющий сертификат для разработки и подписать разреженный пакет с помощью средства [SignTool](https://docs.microsoft.com/windows/msix/package/sign-app-package-using-signtool), которое доступно в Windows SDK.

В следующем примере показано, как подписать разреженный пакет из командной строки.

```Console
SignTool.exe sign /fd SHA256 /a /f <path to certificate>\MyCertificate.pfx /p <certificate password> <path to sparse package>\MyPackage.msix
```

### <a name="add-the-package-identity-metadata-to-your-desktop-application-manifest"></a>Добавление метаданных идентификатора пакета в манифест классического приложения

Необходимо также добавить [параллельный манифест приложения](https://docs.microsoft.com/windows/win32/sbscs/application-manifests) для классического приложения, а также элемент [&lt;msix&gt;](https://docs.microsoft.com/windows/win32/sbscs/application-manifests#msix) с помощью атрибутов, которые объявляют атрибуты идентификатора приложения. Значения этих атрибутов использует операционная система для определения идентификатора приложения при запуске исполняемого файла.

В следующем примере показан параллельный манифест приложения с элементом **\<msix\>** .

```xml
<?xml version="1.0" encoding="utf-8"?>
<assembly manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1">
  <assemblyIdentity version="1.0.0.0" name="Contoso.PhotoStoreApp"/>
  <msix xmlns="urn:schemas-microsoft-com:msix.v1"
          publisher="CN=Contoso"
          packageName="ContosoPhotoStore"
          applicationId="ContosoPhotoStore"
        />
</assembly>
```

Атрибуты элемента **\<msix\>** должны соответствовать таким значениям в манифесте пакета для разреженного пакета:

* Атрибуты **packageName** и **publisher** должны соответствовать атрибутам **Name** и **Publisher** в элементе [ **\<Identity\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) в манифесте пакета соответственно.
* Атрибут **applicationId** должен соответствовать атрибуту **Id** элемента [ **\<Application\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) в манифесте пакета.

Параллельный манифест приложения должен находиться в том же каталоге, что и исполняемый файл для классического приложения, и по умолчанию его имя должно совпадать с именем исполняемого файла вашего приложения с добавленным к нему расширением `.manifest`. Например, если имя исполняемого файла приложения — `ContosoPhotoStore`, то имя файла манифеста приложения должно быть `ContosoPhotoStore.exe.manifest`.

## <a name="register-your-sparse-package-at-run-time"></a>Регистрация разреженного пакета во время выполнения

Чтобы предоставить идентификатор пакета для классического приложения, ваше приложение должно зарегистрировать разреженный пакет с использованием класса [PackageManager](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager). Вы можете добавить код в приложение, чтобы регистрировать разреженный пакет при первом запуске приложения или во время установки классического приложения (например, если вы используете MSI для установки классического приложения, этот код можно запускать по настраиваемому действию).

В следующем примере показано, как зарегистрировать разреженный пакет. Этот код создает объект **AddPackageOptions**, содержащий путь к внешнему расположению, на которое манифест пакета может ссылаться, чтобы указать содержимое вне пакета. Затем код передает этот объект в метод **PackageManager.AddPackageByUriAsync** для регистрации разреженного пакета. Этот метод также получает расположение подписанного разреженного пакета в виде универсального кода ресурса (URI). Более полный пример см. в файле кода `StartUp.cs` в связанном [примере](#sample).

```csharp
private static bool registerSparsePackage(string externalLocation, string sparsePkgPath)
{
    bool registration = false;
    try
    {
        Uri externalUri = new Uri(externalLocation);
        Uri packageUri = new Uri(sparsePkgPath);

        Console.WriteLine("exe Location {0}", externalLocation);
        Console.WriteLine("msix Address {0}", sparsePkgPath);

        Console.WriteLine("  exe Uri {0}", externalUri);
        Console.WriteLine("  msix Uri {0}", packageUri);

        PackageManager packageManager = new PackageManager();

        // Declare use of an external location
        var options = new AddPackageOptions();
        options.ExternalLocationUri = externalUri;

        Windows.Foundation.IAsyncOperationWithProgress<DeploymentResult, DeploymentProgress> deploymentOperation = packageManager.AddPackageByUriAsync(packageUri, options);

        // Other progress and error-handling code omitted for brevity...
    }
}
```

## <a name="sample"></a>Пример

Полнофункциональный пример приложения, демонстрирующий предоставление идентификатора пакета для классического приложения с помощью разреженного пакета, см. в разделе [https://github.com/microsoft/AppModelSamples/tree/master/Samples/SparsePackages](https://github.com/microsoft/AppModelSamples/tree/master/Samples/SparsePackages). Дополнительные сведения о сборке и запуске примера см. в [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/10/29/identity-registration-and-activation-of-non-packaged-win32-apps/#HBMFEM843XORqOWx.97).

Этот пример содержит следующие компоненты:

* Исходный код для приложения WPF с именем PhotoStoreDemo. Во время запуска приложение проверяет, работает ли оно с идентификатором. Если приложение работает без идентификатора, оно регистрирует разреженный пакет, а затем выполняется перезапуск приложения. Дополнительную информацию о коде, который выполняет эти действия, см. в `StartUp.cs`.
* Параллельный манифест приложения с именем `PhotoStoreDemo.exe.manifest`.
* Манифест пакета с именем `AppxManifest.xml`.

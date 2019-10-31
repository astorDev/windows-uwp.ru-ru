---
Description: Узнайте, как предоставить удостоверение для неупакованных классических приложений, чтобы в этих приложениях можно было использовать современные функции Windows 10.
title: Предоставление удостоверений для неупакованных классических приложений
ms.date: 10/25/2019
ms.topic: article
keywords: Windows 10, Настольный компьютер, пакет, удостоверение, MSIX, Win32
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: f355bba3087f58ed20800052371804048bc0006c
ms.sourcegitcommit: d7eccdb27c22bccac65bd014e62b6572a6b44602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145619"
---
# <a name="grant-identity-to-non-packaged-desktop-apps"></a>Предоставление удостоверений для неупакованных классических приложений

<!--
> [!NOTE]
> The features described in this article require Windows 10 Insider Preview Build 10.0.19000.0 or a later release.
-->

Для многих функций расширяемости Windows 10 требуется [удостоверение пакета](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) из настольных приложений не под управлением UWP, включая фоновые задачи, уведомления, живые плитки и общие целевые объекты. В этих сценариях ОС требуется удостоверение, чтобы он мог идентифицировать вызывающий объект соответствующего API.

В выпусках ОС, предшествующих предварительной версии Windows 10 Insider Preview Build 10.0.19000.0, единственный способ предоставить удостоверение для классического приложения — [упаковать его в подписанный пакет MSIX](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root). Для этих приложений удостоверение указывается в манифесте пакета, а регистрация удостоверения обрабатывается конвейером развертывания MSIX на основе информации в манифесте. Все содержимое, на которое ссылается манифест пакета, содержится в пакете MSIX.

Начиная с Windows 10 Insider Preview Build 10.0.19000.0, вы можете предоставить удостоверение пакета для настольных приложений, которые не упакованы в пакет MSIX, создав и зарегистрировав в приложении *разреженный пакет* . Эта поддержка позволяет настольным приложениям, которые еще не смогли внедрить MSIX упаковку для развертывания, использовать функции расширяемости Windows 10, для которых требуется удостоверение пакета. Дополнительные сведения см. в [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/10/29/identity-registration-and-activation-of-non-packaged-win32-apps/#HBMFEM843XORqOWx.97).

Чтобы создать и зарегистрировать разреженный пакет, который предоставляет удостоверение пакета для классического приложения, выполните следующие действия.

1. [Создание манифеста пакета для разреженного пакета](#create-a-package-manifest-for-the-sparse-package)
2. [Сборка и подписывание разреженного пакета](#build-and-sign-the-sparse-package)
3. [Добавление метаданных удостоверения пакета в манифест приложения для настольных систем](#add-the-package-identity-metadata-to-your-desktop-application-manifest)
4. [Регистрация разреженного пакета во время выполнения](#register-your-sparse-package-at-run-time)

## <a name="important-concepts"></a>Важные понятия

Следующие функции позволяют неупакованным классическим приложениям получать удостоверение пакета.

### <a name="sparse-packages"></a>Разреженные пакеты

*Разреженный пакет* содержит манифест пакета, но не имеет других двоичных файлов и содержимого приложения. Манифест разреженного пакета может ссылаться на файлы за пределами пакета в предварительно определенном внешнем расположении. Это позволяет приложениям, которые еще не способны внедрить MSIX упаковку в приложение, получать удостоверение пакета, как это требуется для некоторых функций расширяемости Windows 10.

> [!NOTE]
> Классическое приложение, использующее разреженный пакет, не получает некоторые преимущества полного развертывания с помощью пакета MSIX. К таким преимуществам относятся защита от несанкционированного доступа, установка в заблокированном расположении и полное управление ОС во время развертывания, времени выполнения и удаления.

### <a name="package-external-location"></a>Внешнее расположение пакета

Для поддержки разреженных пакетов схема манифеста пакета теперь поддерживает необязательный элемент **\<алловекстерналконтент\>** в элементе [ **\<свойства\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-properties) . Это позволяет манифесту пакета ссылаться на содержимое вне пакета в определенном расположении на диске.

Например, если имеется существующее неупакованное классическое приложение, устанавливающее исполняемый файл приложения и другое содержимое в папке C:\Program Филес\мидесктопапп\, можно создать разреженный пакет, включающий **\<алловекстерналконтент\>** элемент в манифесте. Во время процесса установки приложения или при первом запуске приложения можно установить разреженный пакет и объявить C:\Program Филес\мидесктопапп\ как внешнее расположение, которое будет использовать приложение.

## <a name="create-a-package-manifest-for-the-sparse-package"></a>Создание манифеста пакета для разреженного пакета

Перед созданием разреженного пакета необходимо сначала создать [манифест пакета](https://docs.microsoft.com/uwp/schemas/appxpackage/appx-package-manifest) (файл с именем appxmanifest. XML), который объявляет метаданные удостоверения пакета для классического приложения и другие необходимые сведения. Самый простой способ создать манифест пакета для разреженного пакета — использовать приведенный ниже пример и настроить его для приложения с помощью [ссылки на схему](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root).

Убедитесь, что манифест пакета включает следующие элементы:

* Элемент [ **\<Identity\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) , описывающий атрибуты идентификации для классического приложения.
* Элемент **\<алловекстерналконтент\>** в элементе [ **\<свойства\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-properties) . Этому элементу должно быть присвоено значение `true`, что позволяет манифесту пакета ссылаться на содержимое вне пакета в определенном расположении на диске. На следующем шаге вы укажете путь к внешнему расположению при регистрации разреженного пакета из кода, который выполняется в установщике или приложении. Любое содержимое, на которое ссылается манифест, не расположенное в самом пакете, должно быть установлено во внешнее расположение.
* Атрибут **MinVersion** элемента [ **\<TargetDeviceFamily\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) должен иметь значение `10.0.19000.0` или более поздней версии.
* Атрибуты **TrustLevel = медиумил** и **рунтимебехавиор = Win32App** элемента [ **\<приложения\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) объявляют, что классическое приложение, связанное с разреженным пакетом, будет выполняться аналогично стандартному неупакованному рабочему столу приложение без параметров виртуализации реестра и файловой системы и других изменений времени выполнения.

В следующем примере показано полное содержимое манифеста разреженного пакета (AppxManifest. XML). Этот манифест включает расширение `windows.sharetarget`, для которого требуется удостоверение пакета.

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

После создания манифеста пакета создайте разреженный пакет с помощью [средства программе makeappx. exe](https://docs.microsoft.com/windows/msix/package/create-app-package-with-makeappx-tool) в Windows SDK. Так как разреженный пакет не содержит файлы, на которые ссылается манифест, необходимо указать параметр `/nv`, который пропускает семантическую проверку для пакета.

В следующем примере показано, как создать разреженный пакет из командной строки.  

```Console
MakeAppx.exe  pack  /d  <path to directory that contains manifest>  /p  <output path>\MyPackage.msix  /nv
```

Прежде чем можно будет успешно установить разреженный пакет на целевой компьютер, необходимо подписать его сертификатом, который является доверенным на целевом компьютере. Можно создать новый самозаверяющий сертификат для целей разработки и подписать разреженный пакет с помощью средства [SignTool](https://docs.microsoft.com/windows/msix/package/sign-app-package-using-signtool), которое доступно в Windows SDK.

В следующем примере показано, как подписать разреженный пакет из командной строки.

```Console
SignTool.exe sign /fd SHA256 /a /f <path to certificate>\MyCertificate.pfx  /p <certificate password>  <path to sparse package>\MyPackage.msix
```

### <a name="add-the-package-identity-metadata-to-your-desktop-application-manifest"></a>Добавление метаданных удостоверения пакета в манифест приложения для настольных систем

Кроме того, необходимо включить в классическое приложение [Манифест параллельного приложения](https://docs.microsoft.com/windows/win32/sbscs/application-manifests) и включить элемент **\<msix\>** с атрибутами, которые объявляют атрибуты удостоверения приложения. Значения этих атрибутов используются операционной системой для определения удостоверения приложения при запуске исполняемого файла.

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

Атрибуты элемента **\<msix\>** должны соответствовать этим значениям в манифесте пакета для разреженного пакета:

* Атрибуты **packageName** и **Publisher** должны соответствовать атрибутам **Name** и **Publisher** в элементе [ **\<Identity\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) в манифесте пакета соответственно.
* Атрибут **applicationId** должен соответствовать атрибуту **ID** элемента [ **\<приложения\>** ](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) в манифесте пакета.

Параллельный манифест приложения должен находиться в том же каталоге, что и исполняемый файл для классического приложения, и по соглашению имя должно совпадать с именем исполняемого файла приложения с добавленным к нему расширением `.manifest`. Например, если имя исполняемого файла приложения — `ContosoPhotoStore`, имя файла манифеста приложения должно быть `ContosoPhotoStore.exe.manifest`.

## <a name="register-your-sparse-package-at-run-time"></a>Регистрация разреженного пакета во время выполнения

Чтобы предоставить удостоверение пакета для классического приложения, приложение должно зарегистрировать разреженный пакет с помощью класса [PackageManager](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager) . Вы можете добавить код в приложение, чтобы зарегистрировать разреженный пакет при первом запуске приложения, или запустить код для регистрации пакета во время установки классического приложения (например, если вы используете MSI для установки классического приложения). Этот код можно выполнить из настраиваемого действия.

В следующем примере показано, как зарегистрировать разреженный пакет. Этот код создает объект **аддпаккажеоптионс** , содержащий путь к внешнему расположению, в котором манифест пакета может ссылаться на содержимое вне пакета. Затем код передает этот объект методу **PackageManager. аддпаккажебюриасинк** для регистрации разреженного пакета. Этот метод также получает расположение подписанного разреженного пакета в виде универсального кода ресурса (URI). Более полный пример см. в файле кода `StartUp.cs` в связанном [образце](#sample).

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

Полностью функциональный пример приложения, демонстрирующий предоставление удостоверений пакета для классического приложения с помощью разреженного пакета, см. в разделе [https://aka.ms/sparsepkgsample](https://aka.ms/sparsepkgsample). Дополнительные сведения о построении и запуске образца см. в [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/10/29/identity-registration-and-activation-of-non-packaged-win32-apps/#HBMFEM843XORqOWx.97).

Этот пример включает в себя следующее:

* Исходный код для приложения WPF с именем Фотосторедемо. Во время запуска приложение проверяет, выполняется ли оно с удостоверением. Если служба не работает с удостоверениями, она регистрирует разреженный пакет, а затем перезапускает приложение. См. `StartUp.cs` для кода, который выполняет эти действия.
* Параллельный манифест приложения с именем `PhotoStoreDemo.exe.manifest`.
* Манифест пакета с именем `AppxManifest.xml`.

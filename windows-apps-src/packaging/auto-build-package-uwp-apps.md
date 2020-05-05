---
title: Настройка автоматических сборок для приложения UWP
description: Узнайте, как настроить автоматические сборки для создания неопубликованных пакетов и пакетов для Store.
ms.date: 07/17/2019
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f9b0d6bd-af12-4237-bc66-0c218859d2fd
ms.localizationpriority: medium
ms.openlocfilehash: 70415c9f3d58625cfdc651ec67c8a9f37c23cffa
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77089500"
---
# <a name="set-up-automated-builds-for-your-uwp-app"></a>Настройка автоматических сборок для приложения UWP

С помощью Azure Pipelines можно создавать автоматизированные сборки для проектов UWP. В этой статье мы рассмотрим различные способы, позволяющие это сделать. Мы также покажем, как выполнить эти задачи с помощью командной строки, чтобы взаимодействовать с другими системами сборки.

## <a name="create-a-new-azure-pipeline"></a>Создание конвейера Azure

[Зарегистрируйтесь в Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up), если вы этого еще не сделали.

Затем создайте конвейер, который можно использовать для сборки исходного кода. Учебник по созданию конвейера для создания репозитория GitHub см. в статье [Create your first pipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml) (Создание первого конвейера). Предложение Azure Pipelines поддерживает типы репозиториев, перечисленные [в этой статье](https://docs.microsoft.com/azure/devops/pipelines/repos).

## <a name="set-up-an-automated-build"></a>Настройка автоматической сборки

Начнем с определения сборки UWP по умолчанию, которое предоставляется в Azure Dev Ops, а затем перейдем к настройке конвейера.

В списке шаблонов определений сборки выберите шаблон **Универсальная платформа Windows**.

![Выбор шаблона для UWP](images/select-yaml-template.png)

Этот шаблон содержит базовую конфигурацию для сборки проекта UWP:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'x86|x64|ARM'
  buildConfiguration: 'Release'
  appxPackageDir: '$(build.artifactStagingDirectory)\AppxPackages\\'

steps:
- task: NuGetToolInstaller@0

- task: NuGetCommand@2
  inputs:
    restoreSolution: '$(solution)'

- task: VSBuild@1
  inputs:
    platform: 'x86'
    solution: '$(solution)'
    configuration: '$(buildConfiguration)'
    msbuildArgs: '/p:AppxBundlePlatforms="$(buildPlatform)" /p:AppxPackageDir="$(appxPackageDir)" /p:AppxBundle=Always /p:UapAppxPackageBuildMode=StoreUpload'

```

Шаблон по умолчанию пытается подписать пакет сертификатом, который указан в CSPROJ-файле. Если вы хотите подписать пакет во время сборки, вам потребуется доступ к закрытому ключу. В противном случае подписывание можно отключить, добавив параметр `/p:AppxPackageSigningEnabled=false` в раздел `msbuildArgs` YAML-файла.

## <a name="add-your-project-certificate-to-the-secure-files-library"></a>Добавление сертификата проекта в библиотеку защищенных файлов

Не следует отправлять сертификаты в репозиторий, если это возможно. По умолчанию GIT игнорирует их. Для управления безопасной обработкой конфиденциальных файлов, таких как сертификаты, Azure DevOps поддерживает функцию [защищенных файлов](https://docs.microsoft.com/azure/devops/pipelines/library/secure-files?view=azure-devops).

Чтобы отправить сертификат для автоматизированной сборки, выполните следующие действия.

1. В Azure Pipelines разверните узел **Конвейеры** в области навигации и щелкните элемент **Библиотека**.
2. Перейдите на вкладку **Защищенные файлы** и щелкните **+ Secure file** (+ Защищенный файл).

    ![Отправка защищенного файла](images/secure-file1.png)

3. Перейдите к файлу сертификата и нажмите кнопку **ОК**.
4. После отправки сертификата выберите его и просмотрите его свойства. В разделе **Разрешения конвейера** включите переключатель **Авторизовать использование во всех конвейерах**.

    ![Отправка защищенного файла](images/secure-file2.png)

5. Если у закрытого ключа в сертификате есть пароль, рекомендуется сохранить пароль в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates), а затем связать пароль с [группой переменных](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups). Для доступа к паролю из конвейера можно использовать переменную. Обратите внимание, что пароль поддерживается только для закрытого ключа. Использование файла сертификата, защищенного паролем, сейчас не поддерживается.

> [!NOTE]
> Начиная с Visual Studio 2019 временный сертификат больше не создается в проектах UWP. Чтобы создать или экспортировать сертификаты, используйте командлеты PowerShell, описанные в [этой статье](/windows/msix/package/create-certificate-package-signing).

## <a name="configure-the-build-solution-build-task"></a>Настройка задачи построения "Сборка решения"

Эта задача компилирует в двоичные файлы все решения, размещенные в рабочей папке, и создает выходной файл пакета приложения. Эта задача использует аргументы MSBuild. Вам необходимо указать значение этих аргументов. Руководствуйтесь следующей таблицей.

|**Аргумент MSBuild**|**Значение**|**Описание**|
|--------------------|---------|---------------|
| AppxPackageDir | $(Build.ArtifactStagingDirectory)\AppxPackages | Определяет папку для хранения созданных артефактов. |
| AppxBundlePlatforms | $(Build.BuildPlatform) | Позволяет указать платформы, в которые будет включен пакет. |
| AppxBundle | Всегда | Создает объединенный пакет MSIXBUNDLE или APPXBUNDLE с файлами MSIX или APPX для указанной платформы. |
| UapAppxPackageBuildMode | StoreUpload | Создает файл MSIXBUNDLE или APPXBUNDLE и папку **_Test** для загрузки неопубликованных приложений. |
| UapAppxPackageBuildMode | CI | Создает только файл MSIXBUNDLE или APPXBUNDLE. |
| UapAppxPackageBuildMode | SideloadOnly | Создает папку **_Test** только для загрузки неопубликованных приложений. |
| AppxPackageSigningEnabled | верно | Включает подписывание пакетов. |
| PackageCertificateThumbprint | Отпечаток сертификата | Это значение **должно** соответствовать отпечатку в сертификате для подписи, или строка должна быть пустой. |
| PackageCertificateKeyFile | Путь | Путь к файлу сертификата. Это значение извлекается из метаданных защищенного файла. |
| PackageCertificatePassword | Пароль | Пароль для закрытого ключа в сертификате. Рекомендуется сохранить пароль в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) и связать пароль с [группой переменных](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups). Можно передать переменную в этот аргумент. |

### <a name="configure-the-build"></a>Настройка сборки

Если вы хотите выполнить сборку решения из командной строки или с помощью другой системы сборки, запустите MSBuild с этими аргументами.

```powershell
/p:AppxPackageDir="$(Build.ArtifactStagingDirectory)\AppxPackages\\"
/p:UapAppxPackageBuildMode=StoreUpload
/p:AppxBundlePlatforms="$(Build.BuildPlatform)"
/p:AppxBundle=Always
```

### <a name="configure-package-signing"></a>Настройка подписывания пакета

Чтобы подписать пакет MSIX (или APPX), конвейеру необходимо получить сертификат для подписи. Для этого добавьте задачу DownloadSecureFile перед задачей VSBuild.
Это обеспечит доступ к сертификату для подписи с помощью ```signingCert```.

```yml
- task: DownloadSecureFile@1
  name: signingCert
  displayName: 'Download CA certificate'
  inputs:
    secureFile: '[Your_Pfx].pfx'
```

Затем обновите задачу VSBuild, чтобы она ссылалась на сертификат для подписи:

```yml
- task: VSBuild@1
  inputs:
    platform: 'x86'
    solution: '$(solution)'
    configuration: '$(buildConfiguration)'
    msbuildArgs: '/p:AppxBundlePlatforms="$(buildPlatform)" 
                  /p:AppxPackageDir="$(appxPackageDir)" 
                  /p:AppxBundle=Always 
                  /p:UapAppxPackageBuildMode=StoreUpload 
                  /p:AppxPackageSigningEnabled=true
                  /p:PackageCertificateThumbprint="" 
                  /p:PackageCertificateKeyFile="$(signingCert.secureFilePath)"'
```

> [!NOTE]
> В качестве меры предосторожности для аргумента PackageCertificateThumbprint намеренно задана пустая строка. Если отпечаток задан в проекте, но не соответствует сертификату для подписи, сборка завершится ошибкой: `Certificate does not match supplied signing thumbprint`.

### <a name="review-parameters"></a>Проверка параметров

Параметры, определенные с использованием синтаксиса `$()` — это переменные, заданные в определении сборки, которые меняются в других системах сборки.

![переменные по умолчанию](images/building-screen5.png)

Все предопределенные переменные см. в статье [Use predefined variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables) (Предварительно заданные переменные сборки).

## <a name="configure-the-publish-build-artifacts-task"></a>Настройка задачи публикации артефактов сборки

Конвейер UWP по умолчанию не сохраняет созданные артефакты. Чтобы добавить возможность публикации в определение YAML, добавьте следующие задачи.

```yml
- task: CopyFiles@2
  displayName: 'Copy Files to: $(build.artifactstagingdirectory)'
  inputs:
    SourceFolder: '$(system.defaultworkingdirectory)'
    Contents: '**\bin\$(BuildConfiguration)\**'
    TargetFolder: '$(build.artifactstagingdirectory)'

- task: PublishBuildArtifacts@1
  displayName: 'Publish Artifact: drop'
  inputs:
    PathtoPublish: '$(build.artifactstagingdirectory)'
```

Созданные артефакты можно просмотреть в параметре **Артефакты** на странице результатов сборки.

![артефакты](images/building-screen6.png)

Так как мы присвоили аргументу `UapAppxPackageBuildMode` значение `StoreUpload`, папка артефактов содержит пакет для отправки в Store (.msixupload/.appxupload). Обратите внимание, что в Store можно также отправить стандартный пакет приложения (.msix/appx) или пакет приложений (.msixbundle/.appxbundle/). В этой статье мы будем использовать файл .appxupload.

## <a name="address-bundle-errors"></a>Устранение ошибок в пакете приложений

Если добавить в решение несколько проектов UWP и попробовать создать пакет, может возникнуть следующая ошибка:

  `MakeAppx(0,0): Error : Error info: error 80080204: The package with file name "AppOne.UnitTests_0.1.2595.0_x86.appx" and package full name "8ef641d1-4557-4e33-957f-6895b122f1e6_0.1.2595.0_x86__scrj5wvaadcy6" is not valid in the bundle because it has a different package family name than other packages in the bundle`

Эта ошибка возникает, поскольку на уровне решения неясно, какое приложение должно быть в пакете. Чтобы решить эту проблему, откройте каждый файл проекта и добавьте следующие свойства в конец первого элемента `<PropertyGroup>`.

|**Проект**|**Свойства**|
|-------|----------|
|Приложение|`<AppxBundle>Always</AppxBundle>`|
|UnitTests|`<AppxBundle>Never</AppxBundle>`|

Затем удалите аргумент MSBuild `AppxBundle` из этапа сборки.

## <a name="related-topics"></a>Связанные темы

- [Сборка приложения .NET для Windows](https://docs.microsoft.com/vsts/build-release/get-started/dot-net)
- [Формирование пакетов приложений UWP](/windows/msix/package/packaging-uwp-apps)
- [Загрузка неопубликованных бизнес-приложений в Windows 10](https://docs.microsoft.com/windows/deploy/sideload-apps-in-windows-10)
- [Создание сертификата для подписания пакета](/windows/msix/package/create-certificate-package-signing)

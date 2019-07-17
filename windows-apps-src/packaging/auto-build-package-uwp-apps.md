---
title: Настройка автоматических сборок для приложения UWP
description: Узнайте, как настроить автоматические сборки для создания неопубликованных пакетов и пакетов для Store.
ms.date: 09/30/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f9b0d6bd-af12-4237-bc66-0c218859d2fd
ms.localizationpriority: medium
ms.openlocfilehash: 5837674f2cb20710a59eeac0af59498bf28b197e
ms.sourcegitcommit: a86d0bd1c2f67e5986cac88a98ad4f9e667cfec5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68229379"
---
# <a name="set-up-automated-builds-for-your-uwp-app"></a>Настройка автоматических сборок для приложения UWP

Конвейеры Azure можно использовать для создания автоматизированных сборок для проектов универсальной платформы Windows. В этой статье мы рассмотрим различные способы сделать это. Мы также покажем способы выполнения этих задач с помощью командной строки, таким образом, можно интегрировать с любой другой системе построения.

## <a name="create-a-new-azure-pipeline"></a>Создайте конвейер Azure

Для начала [регистрацией Azure конвейеры](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up) Если это еще не сделано.

Создайте конвейер, который можно использовать для сборки исходного кода. Руководство по Создание конвейера для создания репозитория GitHub, см. в разделе [создать свой первый конвейер](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml). Конвейеры Azure поддерживает репозитории типов, перечисленных [в этой статье](https://docs.microsoft.com/azure/devops/pipelines/repos).

## <a name="set-up-an-automated-build"></a>Настройка автоматической сборки

Мы начнем с универсальной платформы Windows, доступный в Azure Dev Ops определение сборки и затем продемонстрируем способ настройки конвейера по умолчанию.

В списке шаблонов определений сборки выберите шаблон **Универсальная платформа Windows**.

![Выберите шаблон универсальной платформы Windows](images/select-yaml-template.png)

Этот шаблон включает базовую конфигурацию для построения проекта универсальной платформы Windows:

```yml
trigger:
- master

pool:
  vmImage: 'VS2017-Win2016'

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

Шаблон по умолчанию предпринимается попытка подписать пакет с помощью сертификата, указанного в CSPROJ-файл. Если вы хотите подписать свой пакет во время сборки необходимо иметь доступ к закрытому ключу. В противном случае вы можете отключить подписывание, добавив параметр `/p:AppxPackageSigningEnabled=false` для `msbuildArgs` раздел в файле YAML.

## <a name="add-your-project-certificate-to-the-secure-files-library"></a>Добавить сертификат проекта на библиотеку защищенных файлов

Следует избегать отправки сертификатов в репозиторий, если это возможно, и их игнорирует git по умолчанию. Чтобы управлять безопасной обработки конфиденциальные файлы, такие как сертификаты, поддерживает Azure DevOps [защиты файлов](https://docs.microsoft.com/azure/devops/pipelines/library/secure-files?view=azure-devops).

Чтобы отправить сертификат для автоматической сборки:

1. В конвейерах Azure разверните **конвейеры** на панели навигации щелкните **библиотеки**.
2. Нажмите кнопку **защиты файлов** вкладке и нажмите кнопку **+ защитного файла**.

    ![как отправить защитный файл](images/secure-file1.png)

3. Перейдите к файлу сертификата и нажмите кнопку **ОК**.
4. После передачи сертификата, выберите его, чтобы просмотреть его свойства. В разделе **конвейера разрешения**, включить **авторизовать для использования в все конвейеры** переключателя.

    ![как отправить защитный файл](images/secure-file2.png)

## <a name="configure-the-build-solution-build-task"></a>Настройка задачи построения "Сборка решения"

Эта задача компилирует любое решение, которое находится в рабочую папку для двоичных файлов и создает выходной файл пакета приложения.
Эта задача использует аргументы MSBuild. Вам необходимо указать значение этих аргументов. Руководствуйтесь следующей таблицей.

|**Аргумент MSBuild**|**Значение**|**Описание**|
|--------------------|---------|---------------|
| AppxPackageDir | $(Build.ArtifactStagingDirectory)\AppxPackages | Определяет папку для хранения созданных артефактов. |
| AppxBundlePlatforms | $(Build.BuildPlatform) | Позволяет определить платформ для включения в пакет. |
| AppxBundle | Всегда | Создает.msixbundle/.appxbundle с файлами.msix/.appx для указана платформа. |
| UapAppxPackageBuildMode | StoreUpload | Создает файл.msixupload/.appxupload и **_Test** папки для загрузки неопубликованных приложений. |
| UapAppxPackageBuildMode | CI | Создает файл.msixupload/.appxupload только. |
| UapAppxPackageBuildMode | SideloadOnly | Создает **_Test** папку для только для корпоративных пользователей. |
| AppxPackageSigningEnabled | true | Включает Подписание пакета. |
| PackageCertificateThumbprint | Отпечаток сертификата | Это значение **необходимо** соответствует отпечаток в сертификате подписи, или быть пустой строкой. |
| PackageCertificateKeyFile | Path | Путь к сертификату для использования. Эти данные извлекаются из метаданных защитного файла. |

### <a name="configure-the-build"></a>Настройка сборки

Если вы хотите создать решение с помощью командной строки или с помощью любой другой системе сборки, запустите MSBuild с этими аргументами.

```powershell
/p:AppxPackageDir="$(Build.ArtifactStagingDirectory)\AppxPackages\\"
/p:UapAppxPackageBuildMode=StoreUpload
/p:AppxBundlePlatforms="$(Build.BuildPlatform)"
/p:AppxBundle=Always
```

### <a name="configure-package-signing"></a>Настройка подписывания пакета

Для подписи пакета MSIX (или APPX) конвейера необходимо получить сертификат для подписи. Чтобы сделать это, добавьте задачу DownloadSecureFile до VSBuild задачи.
Это даст вам доступ подписи сертификата через ```signingCert```.

```yml
- task: DownloadSecureFile@1
  name: signingCert
  displayName: 'Download CA certificate'
  inputs:
    secureFile: '[Your_Pfx].pfx'
```

Затем обновите VSBuild задачи для ссылки на сертификат для подписи:

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
> Аргумент PackageCertificateThumbprint намеренно задать пустую строку в качестве меры предосторожности. Если отпечаток устанавливается в проекте, но не соответствует подписи сертификата, построение завершится ошибкой с ошибкой: `Certificate does not match supplied signing thumbprint`.

### <a name="review-parameters"></a>Проверьте параметры

Параметры, определенные с помощью `$()` синтаксис переменные, определенные в определении сборки, и создавайте будет изменений в других системах.

![переменные по умолчанию](images/building-screen5.png)

Чтобы просмотреть все предопределенные переменные, см. в разделе [предварительно определенные переменные сборки](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="configure-the-publish-build-artifacts-task"></a>Настройка задачи «опубликовать артефакты сборки»

Конвейер универсальной платформы Windows по умолчанию не сохраняет созданные артефакты. Чтобы добавить возможности публикации в определение YAML, добавьте следующие задачи.

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

Вы увидите созданные артефакты в **артефакты** параметр построения «результаты».

![artifacts](images/building-screen6.png)

Так как мы задали `UapAppxPackageBuildMode` аргумент `StoreUpload`, папке артефактов относятся пакеты для отправки в Store (.msixupload/.appxupload). Обратите внимание на то, что можно также отправить регулярных приложения пакета (.msix/.appx) или набор приложений (.msixbundle/.appxbundle/) Store. В этой статье мы будем использовать файл .appxupload.

## <a name="address-bundle-errors"></a>Устранить проблемы с пакета

Если добавить более одного проекта UWP в решение и затем попытайтесь создать пакет, может появиться ошибка следующего вида.

  `MakeAppx(0,0): Error : Error info: error 80080204: The package with file name "AppOne.UnitTests_0.1.2595.0_x86.appx" and package full name "8ef641d1-4557-4e33-957f-6895b122f1e6_0.1.2595.0_x86__scrj5wvaadcy6" is not valid in the bundle because it has a different package family name than other packages in the bundle`

Эта ошибка возникает, поскольку на уровне решения неясно, какое приложение должно быть в пакете. Чтобы устранить эту проблему, откройте каждый файл проекта и добавьте следующие свойства в конце первой `<PropertyGroup>` элемент.

|**Проект**|**Свойства**|
|-------|----------|
|Приложение|`<AppxBundle>Always</AppxBundle>`|
|UnitTests|`<AppxBundle>Never</AppxBundle>`|

Удалите `AppxBundle` аргумент MSBuild на этапе построения.

## <a name="related-topics"></a>См. также

- [Создание приложения .NET для Windows](https://docs.microsoft.com/vsts/build-release/get-started/dot-net)
- [Упаковка приложений универсальной платформы Windows](https://docs.microsoft.com/windows/uwp/packaging/packaging-uwp-apps)
- [Загрузка неопубликованных бизнес-приложений в Windows 10](https://docs.microsoft.com/windows/deploy/sideload-apps-in-windows-10)
- [Создание сертификата для подписи пакета](https://docs.microsoft.com/windows/uwp/packaging/create-certificate-package-signing)

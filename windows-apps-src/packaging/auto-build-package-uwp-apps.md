---
title: Настройка автоматических сборок для приложения UWP
description: Узнайте, как настроить автоматические сборки для создания неопубликованных пакетов и пакетов для Store.
ms.date: 07/17/2019
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f9b0d6bd-af12-4237-bc66-0c218859d2fd
ms.localizationpriority: medium
ms.openlocfilehash: 9df150d4a8873630a371fa2ad02e8c88bed7f42e
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867740"
---
# <a name="set-up-automated-builds-for-your-uwp-app"></a>Настройка автоматических сборок для приложения UWP

Azure Pipelines можно использовать для создания автоматизированных сборок для проектов UWP. В этой статье мы рассмотрим различные способы этого. Мы также покажем, как выполнять эти задачи с помощью командной строки, чтобы можно было интегрироваться с любой другой системой сборки.

## <a name="create-a-new-azure-pipeline"></a>Создание нового конвейера Azure

Начните с [регистрации Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up) , если вы еще не сделали этого.

Затем создайте конвейер, который можно использовать для построения исходного кода. Руководство по созданию конвейера для создания репозитория GitHub см. в статье [Создание первого конвейера](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml). Azure Pipelines поддерживает типы репозитория, перечисленные [в этой статье](https://docs.microsoft.com/azure/devops/pipelines/repos).

## <a name="set-up-an-automated-build"></a>Настройка автоматической сборки

Мы начнем с определения сборки UWP по умолчанию, которое доступно в операциях разработки Azure, а затем покажу, как настроить конвейер.

В списке шаблонов определений сборки выберите шаблон **Универсальная платформа Windows**.

![Выберите шаблон UWP](images/select-yaml-template.png)

Этот шаблон включает базовую конфигурацию для создания проекта UWP:

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

Шаблон по умолчанию пытается подписать пакет сертификатом, указанным в файле CSPROJ. Если вы хотите подписать пакет во время сборки, необходимо иметь доступ к закрытому ключу. В противном случае подписывание можно отключить, добавив `/p:AppxPackageSigningEnabled=false` параметр `msbuildArgs` в раздел файла YAML.

## <a name="add-your-project-certificate-to-the-secure-files-library"></a>Добавление сертификата проекта в библиотеку защищенных файлов

Следует избегать отправки сертификатов в репозиторий, если это возможно, и Git по умолчанию игнорирует их. Для управления безопасной обработкой конфиденциальных файлов, таких как сертификаты, Azure DevOps поддерживает функцию [защиты файлов](https://docs.microsoft.com/azure/devops/pipelines/library/secure-files?view=azure-devops) .

Чтобы отправить сертификат для автоматизированной сборки, выполните следующие действия.

1. В Azure Pipelines разверните узел **конвейеры** в области навигации и выберите пункт **Библиотека**.
2. Перейдите на вкладку **безопасные файлы** и щелкните **+ безопасный файл**.

    ![Отправка защищенного файла](images/secure-file1.png)

3. Перейдите к файлу сертификата и нажмите кнопку **ОК**.
4. После отправки сертификата выберите его, чтобы просмотреть его свойства. В разделе **разрешения конвейера**установите переключатель Разрешить **использование во всех конвейерах** .

    ![Отправка защищенного файла](images/secure-file2.png)

5. Если закрытый ключ в сертификате имеет пароль, рекомендуется сохранить пароль в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) а затем связать пароль с [группой переменных](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups). Для доступа к паролю из конвейера можно использовать переменную. Обратите внимание, что пароль поддерживается только для закрытого ключа. Использование файла сертификата, защищенного паролем, в настоящее время не поддерживается.

> [!NOTE]
> Начиная с Visual Studio 2019, временный сертификат больше не создается в проектах UWP. Чтобы создать или экспортировать сертификаты, используйте командлеты PowerShell, описанные в [этой статье](/windows/msix/package/create-certificate-package-signing).

## <a name="configure-the-build-solution-build-task"></a>Настройка задачи построения "Сборка решения"

Эта задача компилирует любое решение, которое находится в рабочей папке, в двоичные файлы и создает выходной файл пакета приложения. Эта задача использует аргументы MSBuild. Вам необходимо указать значение этих аргументов. Руководствуйтесь следующей таблицей.

|**Аргумент MSBuild**|**Значение**|**Описание**|
|--------------------|---------|---------------|
| AppxPackageDir | $(Build.ArtifactStagingDirectory)\AppxPackages | Определяет папку для хранения созданных артефактов. |
| AppxBundlePlatforms | $(Build.BuildPlatform) | Позволяет определить платформы для включения в пакет. |
| AppxBundle | Всегда | Создает файл. msixbundle/. appxbundle с файлами. msix/. appx для указанной платформы. |
| UapAppxPackageBuildMode | StoreUpload | Создает файл. мсиксуплоад/. appxupload и папку **_Test** для загрузки неопубликованных приложений. |
| UapAppxPackageBuildMode | CI | Создает только файл. мсиксуплоад/. appxupload. |
| UapAppxPackageBuildMode | сиделоадонли | Создает папку **_Test** только для загрузки неопубликованных приложений. |
| аппкспаккажесигнинженаблед | true | Включает подписывание пакетов. |
| Соответствующих значению packagecertificatethumbprint | Отпечаток сертификата | Это значение **должно** соответствовать отпечатку в сертификате подписи или быть пустой строкой. |
| паккажецертификатекэйфиле | Path | Путь к используемому сертификату. Это значение извлекается из метаданных защищенного файла. |
| паккажецертификатепассворд | Пароль | Пароль для закрытого ключа в сертификате. Рекомендуется сохранить пароль в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) и связать пароль с [группой переменных](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups). Можно передать переменную в этот аргумент. |

### <a name="configure-the-build"></a>Настройка сборки

Если вы хотите выполнить сборку решения с помощью командной строки или любой другой системы сборки, запустите MSBuild с этими аргументами.

```powershell
/p:AppxPackageDir="$(Build.ArtifactStagingDirectory)\AppxPackages\\"
/p:UapAppxPackageBuildMode=StoreUpload
/p:AppxBundlePlatforms="$(Build.BuildPlatform)"
/p:AppxBundle=Always
```

### <a name="configure-package-signing"></a>Настройка подписи пакета

Чтобы подписать пакет MSIX (или APPX), конвейеру необходимо получить сертификат подписи. Для этого добавьте задачу Довнлоадсекурефиле перед задачей Всбуилд.
Это обеспечит доступ к сертификату подписи через ```signingCert```.

```yml
- task: DownloadSecureFile@1
  name: signingCert
  displayName: 'Download CA certificate'
  inputs:
    secureFile: '[Your_Pfx].pfx'
```

Затем обновите задачу Всбуилд, чтобы она ссылалась на сертификат подписи:

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
> Аргумент соответствующих значению packagecertificatethumbprint намеренно устанавливает пустую строку в качестве меры предосторожности. Если отпечаток задан в проекте, но не соответствует сертификату подписи, сборка завершится ошибкой: `Certificate does not match supplied signing thumbprint`.

### <a name="review-parameters"></a>Проверить параметры

Параметры, определенные с помощью `$()` синтаксиса, являются переменными, определенными в определении сборки, и будут изменены в других системах сборки.

![переменные по умолчанию](images/building-screen5.png)

Сведения о просмотре всех предопределенных переменных см. в разделе [стандартные переменные сборки](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="configure-the-publish-build-artifacts-task"></a>Настройка задачи «публикация артефактов сборки»

Конвейер UWP по умолчанию не сохраняет созданные артефакты. Чтобы добавить возможности публикации в определение YAML, добавьте следующие задачи.

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

Созданные артефакты можно просмотреть в параметре **артефакты** страницы результаты сборки.

![artifacts](images/building-screen6.png)

Так как мы установили `UapAppxPackageBuildMode` для `StoreUpload`аргумента значение, папка артефактов включает пакет для отправки в хранилище (. мсиксуплоад/. appxupload). Обратите внимание, что можно также отправить обычный пакета приложения (. msix/. appx) или набор приложений (msixbundle/. appxbundle/) в хранилище. В этой статье мы будем использовать файл .appxupload.

## <a name="address-bundle-errors"></a>Ошибки пакета адресов

Если добавить в решение несколько проектов UWP, а затем попытаться создать пакет, может появиться сообщение об ошибке, подобное этому.

  `MakeAppx(0,0): Error : Error info: error 80080204: The package with file name "AppOne.UnitTests_0.1.2595.0_x86.appx" and package full name "8ef641d1-4557-4e33-957f-6895b122f1e6_0.1.2595.0_x86__scrj5wvaadcy6" is not valid in the bundle because it has a different package family name than other packages in the bundle`

Эта ошибка возникает, поскольку на уровне решения неясно, какое приложение должно быть в пакете. Чтобы устранить эту проблему, откройте каждый файл проекта и добавьте следующие свойства в конец первого `<PropertyGroup>` элемента.

|**Проектом**|**Свойства**|
|-------|----------|
|Приложение|`<AppxBundle>Always</AppxBundle>`|
|UnitTests|`<AppxBundle>Never</AppxBundle>`|

Затем удалите `AppxBundle` аргумент MSBuild из шага сборки.

## <a name="related-topics"></a>См. также

- [Создание приложения .NET для Windows](https://docs.microsoft.com/vsts/build-release/get-started/dot-net)
- [Упаковка приложений UWP](/windows/msix/package/packaging-uwp-apps)
- [БИЗНЕС-приложения загружать неопубликованные в Windows 10](https://docs.microsoft.com/windows/deploy/sideload-apps-in-windows-10)
- [Создание сертификата для подписи пакета](/windows/msix/package/create-certificate-package-signing)

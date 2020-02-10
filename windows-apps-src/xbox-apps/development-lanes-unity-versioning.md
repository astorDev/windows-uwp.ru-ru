---
title: 'Unity: управление версиями проекта UWP'
description: Управление версиями проекта UWP.
ms.localizationpriority: medium
ms.topic: article
ms.date: 02/08/2017
ms.openlocfilehash: b98fba394fb326d60451f07938504e99a92d764d
ms.sourcegitcommit: 3e7a4f7605dfb4e87bac2d10b6d64f8b35229546
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089490"
---
# <a name="unity-version-control-your-uwp-project"></a>Unity: управление версиями проекта UWP

Еще не собирали игру на Unity для Xbox с помощью универсальной платформы Windows (UWP)?  Сначала ознакомьтесь с разделом [Перенос игр на Unity в UWP на Xbox](development-lanes-unity.md).

Существуют разные причины, по которым стоит добавлять в управление версиями элементы созданных вами каталогов UWP. Одна из них — добавление зависимостей (например, Xbox Live SDK).  Мы используем этот сценарий в качестве примера для этого руководства и надеемся, что он поможет вам решить отдельные задачи вашего проекта.

***Заявление об отказе: мы будем использовать Git в качестве решения для управления версиями.  Если ваше поведение отличается, основные понятия все равно должны быть преобразованы.***

Напомним, как в настоящее время выглядит каталог нашей игры ***ScrapyardPhoenix***:

![Целевая папка сборки](images/build-destination.png)

А вот так выглядит наш каталог UWP:

![Решение VS для UWP](images/uwp-vs-solution.png)

В этом каталоге нас интересует только одна папка — ***ScrapyardPhoenix*** (вставьте сюда название своей игры).  Все остальное в нашей системе управления версиями можно игнорировать.

***Не знакомы с файлом. gitignore?  См. [gitignore](https://git-scm.com/docs/gitignore).***

    ##################################################################
    # The original .gitignore file can be found at
    # https://github.com/github/gitignore/blob/master/Unity.gitignore
    ##################################################################

    # standard ignores for a Unity Project
    ...

    # ignore the whole UWP directory
    /UWP/**

    # except we want to keep... (this line will be modified and removed further down)
    !/UWP/ScrapyardPhoenix/

Попробуем выбрать несколько разных файлов и каталогов из папки **UWP/ScrapyardPhoenix** и добавить их в нашу систему управления версиями.  Сначала подробно рассмотрим весь проект.

![Каталог сборки UWP](images/uwp-build-directory.png)  

## <a name="folders"></a>Папки  

`Assets` | ***включения*** | Содержит Microsoft Store образы  
`Data`   | ***игнорировать*** | Где Unity компилирует проект в (сцены, шейдеры, сценарии, Prefabs и т. д.)  
`Dependencies` | ***включения*** | Эта папка создана для сохранения всех зависимостей UWP в (например, Ксбоксливесдк. dll).  
`Properties` | ***включения*** | Содержит более сложные параметры, которые могут быть изменены разработчиком  
`Unprocessed` | ***игнорировать*** | Содержит `.dll` Unity и файлы `.pdb`  

## <a name="files"></a>Файлы  

`App.cs` | ***включения*** | Точка входа для приложения UWP; его можно изменить и расширить с помощью других исходных файлов  
`Package.appxmanifest` | ***включения*** | Исходный файл манифеста пакета приложения для пакета. msix или. appx  
`project.json` | ***включения*** | Описание пакетов NuGet, от которых зависит ваш `*.csproj`  
`ScrapyardPhoenix.csproj` | ***включения*** | Описывает целевой объект сборки UWP; Если добавить дополнительные зависимости в проект UWP, этот файл `*.csproj` будет содержать эти сведения.  
`ScrapyardPhoenix.csproj.user` | ***игнорировать*** | Этот файл содержит сведения о локальном пользователе

## <a name="resulting-gitignore"></a>Конечный вид файла .gitignore

    ##################################################################
    # The original .gitignore file can be found at
    # https://github.com/github/gitignore/blob/master/Unity.gitignore
    ##################################################################

    # standard ignores for a Unity Project
    ...

    # ignore the whole UWP directory
    /UWP/**

    # except we want to keep...
    !/UWP/ScrapyardPhoenix/Assets/*
    !/UWP/ScrapyardPhoenix/Dependencies/*
    !/UWP/ScrapyardPhoenix/Properties/*
    !/UWP/ScrapyardPhoenix/App.cs
    !/UWP/ScrapyardPhoenix/Package.appxmanifest
    !/UWP/ScrapyardPhoenix/project.json
    !/UWP/ScrapyardPhoenix/ScrapyardPhoenix.csproj

Ну вот и все, теперь члены вашей команды будут синхронизироваться с созданным вами проектом UWP. Теперь вы можете спокойно добавлять дополнительные ресурсы, исходные файлы и зависимости в свой проект UWP.

Другие примеры управления версиями папки UWP можно посмотреть [здесь](https://bitbucket.org/Unity-Technologies/windowsstoreappssamples/overview).

## <a name="adding-dependencies-to-your-uwp-app"></a>Добавление зависимостей в приложение UWP

Добавьте зависимости в DLL и WINMD, разместив их в папке **Ресурсы Unity** внутри папки **Подключаемые модули**, а затем выберите их и установите их параметры целевой платформы соответствующим образом в инспекторе.

![Решение UWP](images/uwp-solution.PNG)

***ScrapyardPhoenix (универсальное приложение для Windows)*** — проект, к которому следует добавлять ссылки (например, Xbox Live SDK).

## <a name="see-also"></a>См. также:
- [Перенос имеющихся игр на Xbox](development-lanes-landing.md)
- [Приложения UWP для Xbox One](index.md)

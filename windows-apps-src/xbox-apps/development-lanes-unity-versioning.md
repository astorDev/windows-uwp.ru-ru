---
title: 'Unity: управление версиями проекта UWP'
description: Управление версиями проекта UWP.
ms.localizationpriority: medium
ms.topic: article
ms.date: 02/08/2017
ms.openlocfilehash: 064eaf42fe7d664be273cd7e2222fa5d90be1a11
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608869"
---
# <a name="unity-version-control-your-uwp-project"></a>Unity: Проект UWP управления версиями

Еще не собирали игру на Unity для Xbox с помощью универсальной платформы Windows (UWP)?  Сначала ознакомьтесь с разделом [Перенос игр на Unity в UWP на Xbox](development-lanes-unity.md).

Существуют разные причины, по которым стоит добавлять в управление версиями элементы созданных вами каталогов UWP. Одна из них — добавление зависимостей (например, Xbox Live SDK).  Мы используем этот сценарий в качестве примера для этого руководства и надеемся, что он поможет вам решить отдельные задачи вашего проекта.

***Отказ от ответственности: Мы будем использовать Git как наше решение управления версиями.  Если ваш отличается, основные понятия по-прежнему нужно преобразовать.***

Напомним, как в настоящее время выглядит каталог нашей игры ***ScrapyardPhoenix***:

![Целевая папка сборки](images/build-destination.png)

А вот так выглядит наш каталог UWP:

![Решение VS для UWP](images/uwp-vs-solution.png)

В этом каталоге нас интересует только одна папка — ***ScrapyardPhoenix*** (вставьте сюда название своей игры).  Все остальное в нашей системе управления версиями можно игнорировать.

***Не знакомы с gitignore-файл является?  См. в разделе [gitignore](https://git-scm.com/docs/gitignore).***

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

`Assets` | ***Включить*** | Содержит изображения Microsoft Store  
`Data`   | ***Игнорировать*** | Где Unity проект компилируется в (сцены, шейдеры, скрипты, Prefabs т. д.)  
`Dependencies` | ***Включить*** | Эта папка является один, созданный мной помнить все зависимости универсальной платформы Windows (например, XboxLiveSDK.dll)  
`Properties` | ***Включить*** | Содержит дополнительные параметры, которые могут быть изменены разработчиком  
`Unprocessed` | ***Игнорировать*** | Содержит Unity `.dll` и `.pdb` файлов  

## <a name="files"></a>Файлы  

`App.cs` | ***Включить*** | Точка входа для приложения универсальной платформы Windows; Это можно изменить и расширить с помощью других исходных файлах  
`Package.appxmanifest` | ***Включить*** | Файл манифеста источника пакета приложения для вашей AppX  
`project.json` | ***Включить*** | Описывает пакеты NuGet вашего `*.csproj` зависит от  
`ScrapyardPhoenix.csproj` | ***Включить*** | Описывает целевой сборки универсальной платформы Windows; При добавлении в UWP Дополнительные зависимости проекта, это `*.csproj` файл будут содержать эти данные  
`ScrapyardPhoenix.csproj.user` | ***Игнорировать*** | Этот файл содержит сведения о локальных пользователях

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

## <a name="see-also"></a>См. также
- [Перенос существующих игры для Xbox](development-lanes-landing.md)
- [Приложения UWP для Xbox One](index.md)

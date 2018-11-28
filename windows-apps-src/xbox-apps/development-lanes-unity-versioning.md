---
title: 'Unity: управление версиями проекта UWP'
description: Управление версиями проекта UWP.
ms.localizationpriority: medium
ms.openlocfilehash: 9fe59b0c63f536822ec50a225bbdc11e68b97713
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7837082"
---
# <a name="unity-version-control-your-uwp-project"></a>Unity: управление версиями проекта UWP

Еще не собирали игру на Unity для Xbox с помощью универсальной платформы Windows (UWP)?  Сначала ознакомьтесь с разделом [Перенос игр на Unity в UWP на Xbox](development-lanes-unity.md).

Существуют разные причины, по которым стоит добавлять в управление версиями элементы созданных вами каталогов UWP. Одна из них — добавление зависимостей (например, Xbox Live SDK).  Мы используем этот сценарий в качестве примера для этого руководства и надеемся, что он поможет вам решить отдельные задачи вашего проекта.

***Сразу оговорим, что в качестве решения для управления версиями мы будем использовать Git.  Если вы используете другое решение, общие принципы все равно остаются теми же.***

Напомним, как в настоящее время выглядит каталог нашей игры ***ScrapyardPhoenix***:

![Целевая папка сборки](images/build-destination.png)

А вот так выглядит наш каталог UWP:

![Решение VS для UWP](images/uwp-vs-solution.png)

В этом каталоге нас интересует только одна папка — ***ScrapyardPhoenix*** (вставьте сюда название своей игры).  Все остальное в нашей системе управления версиями можно игнорировать.

***Не знаете, что представляет собой файл .gitignore?  См. [gitignore](https://git-scm.com/docs/gitignore).***

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

`Assets` | ***Включить*** | Содержит изображения в Microsoft Store  
`Data`   | ***Игнорировать*** | Где Unity компилирует ваш проект (сцены, шейдеры, сценарии, заготовки, и т. д.)  
`Dependencies` | ***Включить*** | Эта папка является тот, который я создал, чтобы сохранить все зависимости UWP (например, xboxlivesdk.dll)  
`Properties` | ***Включить*** | Содержит дополнительные параметры, которые могут быть изменены разработчиком  
`Unprocessed` | ***Игнорировать*** | Содержит Unity `.dll` и `.pdb` файлов  

## <a name="files"></a>Файлы  

`App.cs` | ***Включить*** | Точка входа для приложения UWP. ее можно изменять и дополнять с помощью других исходных файлов  
`Package.appxmanifest` | ***Включить*** | Приложение исходный файл манифест пакета для AppX  
`project.json` | ***Включить*** | Описывает пакеты NuGet вашего `*.csproj` зависит от  
`ScrapyardPhoenix.csproj` | ***Включить*** | Описывает цель сборки UWP; Если вы добавили Дополнительные зависимости для игры UWP проект, этот `*.csproj` файл будет содержать сведения об  
`ScrapyardPhoenix.csproj.user` | ***Игнорировать*** | Этот файл содержит сведения о локальном пользователе

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
- [Перенос существующих игр на Xbox](development-lanes-landing.md)
- [Приложения UWP для Xbox One](index.md)

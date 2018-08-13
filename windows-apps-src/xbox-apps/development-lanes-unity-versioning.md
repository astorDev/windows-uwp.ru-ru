---
author: JordanEllis6809
title: 'Unity: управление версиями проекта UWP'
description: Управление версиями проекта UWP.
ms.openlocfilehash: 3b796c31e6b284cea628ba68a34799cf9317ee2e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.locfileid: "225307"
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

`Assets` | ***Включить*** | Содержит изображения для Магазина Windows.  
`Data`   | ***Игнорировать*** | Расположение, в котором Unity компилирует ваш проект (сцены, шейдеры, сценарии, заготовки и т. д.).  
`Dependencies` | ***Включить*** | Эту папку я создал, чтобы хранить в ней все зависимости UWP (например, XboxLiveSDK.dll).  
`Properties` | ***Включить*** | Содержит дополнительные параметры, которые может изменять разработчик.  
`Unprocessed` | ***Игнорировать*** | Содержит файлы `.dll` и `.pdb` игрового движка Unity.  

## <a name="files"></a>Файлы  

`App.cs` | ***Включить*** | Точка входа приложения UWP. Ее можно изменять и дополнять с помощью других исходных файлов.  
`Package.appxmanifest` | ***Включить*** | Манифест пакета для AppX.  
`project.json` | ***Включить*** | Описывает пакеты NuGet, от которых зависит `*.csproj`.  
`ScrapyardPhoenix.csproj` | ***Включить*** | Описывает цель сборки UWP. Если вы добавили дополнительные зависимости в проект UWP, этот файл `*.csproj` будет содержать сведения об этом.  
`ScrapyardPhoenix.csproj.user` | ***Игнорировать*** | Этот файл содержит сведения о локальном пользователе.

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

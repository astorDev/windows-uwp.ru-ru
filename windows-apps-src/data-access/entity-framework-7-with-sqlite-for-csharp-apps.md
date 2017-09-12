---
author: normesta
ms.assetid: BC7E8130-A28A-443C-8D7E-353E7DA33AE3
description: "EntityFramework (EF)— это объектно-реляционный модуль сопоставления, позволяющий разработчикам работать с реляционными данными с помощью объектов, специализированных для доменов."
title: "Entity framework Core с SQLite для приложений на C#"
ms.author: normesta
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, SQLite, C#, EF, entity framework
ms.openlocfilehash: 9d447b8a197ed7eaea0f991749064912dffb66d7
ms.sourcegitcommit: 378382419f1fda4e4df76ffa9c8cea753d271e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2017
---
# <a name="entity-framework-core-with-sqlite-for-c-apps"></a>Entity Framework Core с SQLite для приложений на C#

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

EntityFramework (EF)— это объектно-реляционный модуль сопоставления, позволяющий разработчикам работать с реляционными данными с помощью объектов, специализированных для доменов. В этой статье объясняется, как использовать EntityFrameworkCore с базой данных SQLite в универсальном приложении для Windows.

Платформа Entity Framework Core была разработана для разработчиков .NET, и ее можно использовать с SQLite на универсальной платформе Windows (UWP) для хранения и использования реляционных данных с помощью объектов, специализированных для доменов. Вы можете перенести код EF из приложения .NET в приложение UWP, и он будет работать после внесения соответствующих изменений в строку подключения.

На текущий момент EF поддерживает SQLite только на UWP. Подробное пошаговое руководство по установке Entity Framework Core и созданию моделей доступно на странице [Начало работы с универсальной платформой Windows](http://go.microsoft.com/fwlink/p/?LinkId=735013). На этой странице рассматриваются следующие темы:

-   Необходимые условия
-   Создание нового проекта
-   Установка Entity Framework
-   Создание модели
-   Создание базы данных
-   Использование модели

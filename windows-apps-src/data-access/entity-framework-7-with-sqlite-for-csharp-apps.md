---
author: mcleblanc
ms.assetid: BC7E8130-A28A-443C-8D7E-353E7DA33AE3
description: "Entity Framework (EF) — это объектно-реляционный модуль сопоставления, позволяющий разработчикам работать с реляционными данными с помощью объектов, специализированных для доменов."
title: "Entity Framework 7 с SQLite для приложений на C#"
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, SQLite, C#, EF, entity framework
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 2ab2a12f6c2bc2f0f8853b404afaf13bf80635b7
ms.lasthandoff: 02/07/2017

---

# <a name="entity-framework-core-1-with-sqlite-for-c-apps"></a>Entity Framework Core 1 с SQLite для приложений на C#

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Entity Framework (EF) — это объектно-реляционный модуль сопоставления, позволяющий разработчикам работать с реляционными данными с помощью объектов, специализированных для доменов. В этой статье объясняется, как использовать Entity Framework Core 1 с базой данных SQLite в универсальном приложении для Windows.

Платформа Entity Framework Core 1 была разработана для разработчиков .NET, и ее можно использовать с SQLite на универсальной платформе Windows (UWP) для хранения и использования реляционных данных с помощью объектов, специализированных для доменов. Вы можете перенести код EF из приложения .NET в приложение UWP, и он будет работать после внесения соответствующих изменений в строку подключения.

На текущий момент EF поддерживает SQLite только на UWP. Подробное пошаговое руководство по установке Entity Framework Core 1 и созданию моделей доступно на странице [Начало работы с универсальной платформой Windows](http://go.microsoft.com/fwlink/p/?LinkId=735013). На этой странице рассматриваются следующие темы:

-   Необходимые условия
-   Создание нового проекта
-   Установка Entity Framework
-   Создание модели
-   Создание базы данных
-   Использование модели


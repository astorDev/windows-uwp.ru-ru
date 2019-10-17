---
title: Приступая к работе с Python в Windows с базой данных
description: Руководство, помогающее приступить к работе с PostgreSQL или MongoDB с Python в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Python, Windows 10, PostgreSQL, MongoDB, postgres, Mongo, Microsoft, Python в Windows, Установка PostgreSQL в Windows, установка MongoDB в Windows, использование PostgreSQL с Python, использование MongoDB с Python, PostgreSQL на WSL, MongoDB на WSL
ms.localizationpriority: medium
ms.date: 07/19/2019
ms.openlocfilehash: 9b1bdea86739f3d58b39cf7f0e6b8090474886f3
ms.sourcegitcommit: 60d2d15dd0d365f82e4e90e4bc34b40cf5b4a247
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517775"
---
# <a name="get-started-using-postgresql-or-mongodb-with-python-on-windows"></a>Приступая к работе с PostgreSQL или MongoDB с Python в Windows

Это пошаговое руководство поможет приступить к подключению приложения Python к базе данных. Мы решили сосредоточиться на двух популярных вариантах: PostgreSQL и MongoDB.

## <a name="differences-between-mongodb-and-postgresql"></a>Различия между MongoDB и PostgreSQL

[!INCLUDE [Postgres vs Mongo](../includes/postgres-v-mongo.md)]

> [!NOTE]
> Вы также можете подумать, как интегрированная платформа и инструменты используются с определенной системой баз данных. Похоже, [веб-платформа Django](./web-frameworks.md#hello-world-tutorial-for-django) лучше интегрирована с PostgreSQL (см. [документацию по Django](https://docs.djangoproject.com/en/2.2/ref/contrib/postgres/) и [psycopg2](https://github.com/psycopg/psycopg2)). Вероятно, [веб-платформа Flask](./web-frameworks.md#hello-world-tutorial-for-flask) лучше интегрирована с MongoDB (см. [монгоенгине](https://github.com/MongoEngine/flask-mongoengine) и [пимонго](https://github.com/dcrosta/flask-pymongo)).

## <a name="install-postgresql"></a>Установка PostgreSQL

[!INCLUDE [Install and run PostgresQL](../includes/install-and-run-postgres.md)]

### <a name="vs-code-support-for-postgresql"></a>Поддержка VS Code PostgreSQL

VS Code поддерживает работу с базами данных PostgreSQL через [расширение PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql), вы можете создавать, подключать, управлять базами данных PostgreSQL и запрашивать их из VS Code.

## <a name="install-mongodb"></a>Установка MongoDB

[!INCLUDE [Install and run Mongo](../includes/install-and-run-mongo.md)]

### <a name="vs-code-support-for-mongodb"></a>Поддержка VS Code MongoDB

VS Code поддерживает работу с базами данных MongoDB с помощью [расширения Azure CosmosDB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb), вы можете создавать, подключать, управлять базами данных MongoDB и запрашивать их из VS Code.

Дополнительные сведения см. в VS Code документах: [Работа с MongoDB](https://code.visualstudio.com/docs/azure/mongodb).

## <a name="set-up-profile-aliases"></a>Настройка псевдонимов профилей

[!INCLUDE [Set up profile aliases](../includes/profile-aliases.md)]

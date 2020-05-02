---
title: Приступая к работе с Python в Windows с использованием базы данных
description: Руководство, помогающее приступить к работе с PostgreSQL или MongoDB с Python в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: python, windows 10, postgresql, mongodb, postgres, mongo, microsoft, python в windows, установка postgresql в windows, установка mongodb в windows, использование postgresql с python, использование mongodb с python, postgresql на WSL, mongodb на WSL
ms.localizationpriority: medium
ms.date: 07/19/2019
ms.openlocfilehash: 9b1bdea86739f3d58b39cf7f0e6b8090474886f3
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72517775"
---
# <a name="get-started-using-postgresql-or-mongodb-with-python-on-windows"></a>Приступая к работе с PostgreSQL или MongoDB с Python в Windows

Это пошаговое руководство поможет приступить к подключению приложения Python к базе данных. Мы решили сосредоточиться на двух популярных вариантах: PostgreSQL и MongoDB.

## <a name="differences-between-mongodb-and-postgresql"></a>Различия между MongoDB и PostgreSQL

[!INCLUDE [Postgres vs Mongo](../includes/postgres-v-mongo.md)]

> [!NOTE]
> Необходимо принять во внимание то, как интегрированы используемые платформа и инструменты с определенной системой баз данных. [Веб-платформа Django](./web-frameworks.md#hello-world-tutorial-for-django) лучше интегрирована с PostgreSQL (см. документацию по [Django](https://docs.djangoproject.com/en/2.2/ref/contrib/postgres/) и [psycopg2](https://github.com/psycopg/psycopg2)). [Веб-платформа Flask](./web-frameworks.md#hello-world-tutorial-for-flask) лучше интегрирована с MongoDB (см. документацию по [MongoEngine](https://github.com/MongoEngine/flask-mongoengine) и [PyMongo](https://github.com/dcrosta/flask-pymongo)).

## <a name="install-postgresql"></a>Установка PostgreSQL

[!INCLUDE [Install and run PostgresQL](../includes/install-and-run-postgres.md)]

### <a name="vs-code-support-for-postgresql"></a>Поддержка VS Code для PostgreSQL

VS Code поддерживает работу с базами данных PostgreSQL с помощью [расширения PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql). Вы можете создавать, запрашивать базы данных PostgreSQL, подключаться к ним, а также управлять ними из VS Code.

## <a name="install-mongodb"></a>Установка MongoDB

[!INCLUDE [Install and run Mongo](../includes/install-and-run-mongo.md)]

### <a name="vs-code-support-for-mongodb"></a>Поддержка VS Code для MongoDB

VS Code поддерживает работу с базами данных MongoDB с помощью [расширения Azure CosmosDB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb). Вы можете создавать, запрашивать базы данных MongoDB, подключаться к ним, а также управлять ими из VS Code.

Дополнительные сведения см. в разделе документации по VS Code, [посвященному работе с MongoDB](https://code.visualstudio.com/docs/azure/mongodb).

## <a name="set-up-profile-aliases"></a>Настройка псевдонимов профилей

[!INCLUDE [Set up profile aliases](../includes/profile-aliases.md)]

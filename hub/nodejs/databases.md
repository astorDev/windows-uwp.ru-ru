---
title: Приступая к работе с подключениями приложений Node.js к базе данных
description: Приступите к работе с подключениями приложений Node.js к базе данных в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: NodeJS, Node.js, windows 10, microsoft, изучение nodejs, node в windows, node в wsl, node в windows для linux, установка node в windows, nodejs с vs code, разработка с использованием node в windows, разработка с использованием nodejs в windows, установка node в WSL, NodeJS в подсистеме Windows для Linux
ms.localizationpriority: medium
ms.date: 09/19/2019
ms.openlocfilehash: 63c47107538d8744201f83ea1be24cfaf3193f4f
ms.sourcegitcommit: 60d2d15dd0d365f82e4e90e4bc34b40cf5b4a247
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517820"
---
# <a name="get-started-using-mongodb-or-postgresql-with-nodejs-on-windows"></a>Приступая к работе с MongoDB или PostgreSQL с Node.js в Windows

Приложениям Node.js часто приходится сохранять данные, для чего могут использоваться файлы, локальное хранилище, облачные службы или базы данных. Это пошаговое руководство поможет приступить к подключению приложения Node.js к базе данных. Мы решили сосредоточиться на двух популярных вариантах: MongoDB и PostgreSQL.

## <a name="prerequisites"></a>Предварительные условия

В этом руководстве предполагается, что вы уже [настроили в среде разработки Node.js подсистему Windows для Linux версии 2](./setup-on-wsl2.md) (WSL 2), в том числе:

- установили Windows 10 Insider Preview (сборки 18932) или более поздней версии;
- включили компонент WSL 2 в Windows;
- установили дистрибутив Linux (Ubuntu 18.04 для наших примеров), что можно проверить с помощью команды `wsl lsb_release -a`;
- убедились, что дистрибутив Ubuntu 18.04 работает в режиме WSL 2 (WSL может запускать дистрибутивы в режиме версии 1 или 2), что можно проверить, открыв PowerShell и выполнив команду `wsl -l -v`;
- с помощью PowerShell установили Ubuntu 18.04 в качестве дистрибутива по умолчанию с помощью команды `wsl -s ubuntu 18.04`.

## <a name="differences-between-mongodb-and-postgresql"></a>Различия между MongoDB и PostgreSQL

[!INCLUDE [Postgres vs Mongo](../includes/postgres-v-mongo.md)]

## <a name="install-mongodb"></a>Установка MongoDB

[!INCLUDE [Install and run Mongo](../includes/install-and-run-mongo.md)]

### <a name="vs-code-support-for-mongodb"></a>Поддержка VS Code для MongoDB

VS Code поддерживает работу с базами данных MongoDB с помощью [расширения Azure CosmosDB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb). Вы можете создавать базы данных MongoDB, отправлять к ним запросы, а также управлять этими базами данных из VS Code.

Дополнительные сведения см. в разделе документации по VS Code, [посвященному работе с MongoDB](https://code.visualstudio.com/docs/azure/mongodb).

Дополнительные сведения см. в документации по MongoDB:

- [Общие сведения об использовании MongoDB](https://docs.mongodb.com/manual/introduction/)
- [Создание пользователей](https://docs.mongodb.com/manual/tutorial/create-users/)
- [Подключение к экземпляру MongoDB на удаленном узле](https://docs.mongodb.com/manual/mongo/#mongodb-instance-on-a-remote-host)
- [CRUD: создание, чтение, обновление и удаление](https://docs.mongodb.com/manual/crud/)
- [Справочная документация](https://docs.mongodb.com/manual/reference/)

## <a name="install-postgresql"></a>Установка PostgreSQL

[!INCLUDE [Install and run PostgresQL](../includes/install-and-run-postgres.md)]

### <a name="vs-code-support-for-postgresql"></a>Поддержка VS Code для PostgreSQL

VS Code поддерживает работу с базами данных PostgreSQL с помощью [расширения PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql). Вы можете создавать, запрашивать базы данных PostgreSQL, подключаться к ним, а также управлять ними из VS Code.

## <a name="set-up-profile-aliases"></a>Настройка псевдонимов профилей

[!INCLUDE [Set up profile aliases](../includes/profile-aliases.md)]

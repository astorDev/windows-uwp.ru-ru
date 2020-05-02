---
ms.topic: include
author: mattwojo
ms.author: mattwoj
ms.date: 10/04/2019
ms.openlocfilehash: f594600991f08a7dfda784ae127be2e6438dacbd
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72314888"
---
Установка MongoDB.

1. Откройте терминал WSL (например, Ubuntu 18.04).
2. Обновите пакеты Ubuntu: `sudo apt update`
3. После обновления пакетов установите MongoDB с помощью: `sudo apt-get install mongodb`
4. Подтвердите установку и получите номер версии: `mongod --version`

После установки MongoDB следует знать 3 команды:

1. `sudo service mongodb status` позволяет проверить состояние базы данных.
2. `sudo service mongodb start` позволяет запустить базу данных.
3. `sudo service mongodb stop` позволяет завершить работу с базой данных.

> [!NOTE]
> Вы можете увидеть команду `sudo systemctl status mongodb`, используемую в учебниках или статьях. Чтобы остаться облегченным, WSL не включает `systemd` (система управления службами в Linux). Вместо этого он использует SysVinit для запуска служб на компьютере. Вы не должны заметить разницы, но если учебник рекомендует использовать `sudo systemctl`, используйте: `sudo /etc/init.d/`. Например, `sudo systemctl status mongodb`для WSL будет `sudo /etc/inid.d/mongodb status`... или вы также можете использовать `sudo service mongodb status`.

### <a name="run-your-mongo-database-in-a-local-server"></a>Запуск базы данных Mongo на локальном сервере

1. Проверка состояния базы данных: `sudo service mongodb status` Вы увидите ответ [Fail], если еще не начали работу с базой данных.

2. Запустите базу данных: `sudo service mongodb start` Теперь должен отобразиться ответ [ОК].

3. Проверьте, подключившись к серверу базы данных и выполнив команду диагностики: `mongo --eval 'db.runCommand({ connectionStatus: 1 })'` Будет выведена текущая версия базы данных, адрес и порт сервера, а также выходные данные команды состояния. Значение `1` в поле "ОК" в ответе указывает на то, что сервер работает.

4. Чтобы предотвратить запуск службы MongoDB, введите: `sudo service mongodb stop`

> [!NOTE]
> MongoDB имеет несколько параметров по умолчанию, включая хранение данных в /data/db и выполнение на порте 27017. Кроме того, `mongod` является управляющей программой (хост-процессом для базы данных), а `mongo` — оболочкой командной строки, которая подключается к конкретному экземпляру `mongod`.

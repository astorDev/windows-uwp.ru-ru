---
ms.topic: include
author: mattwojo
ms.author: mattwoj
ms.date: 10/04/2019
ms.openlocfilehash: f594600991f08a7dfda784ae127be2e6438dacbd
ms.sourcegitcommit: 13faf9dab9946295986f8edd79b5fae0db4ed0f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72314888"
---
Чтобы установить MongoDB, выполните следующие действия.

1. Откройте терминал WSL (Internet Explorer). Ubuntu 18,04).
2. Обновите пакеты Ubuntu: `sudo apt update`
3. После обновления пакетов установите MongoDB с помощью: `sudo apt-get install mongodb`.
4. Подтвердите установку и получите номер версии: `mongod --version`

Есть три команды, которые необходимо знать после установки MongoDB:

1. `sudo service mongodb status` для проверки состояния базы данных.
2. `sudo service mongodb start`, чтобы начать работу с базой данных.
3. `sudo service mongodb stop` для завершения работы базы данных.

> [!NOTE]
> Вы можете увидеть команду `sudo systemctl status mongodb`, которая используется в учебниках или статьях. Чтобы остаться легковесными, WSL не включает `systemd` (система управления службами в Linux). Вместо этого он использует Сисвинит для запуска служб на компьютере. Вы не должны заметить разницы, но если в учебнике рекомендуется использовать `sudo systemctl`, используйте: `sudo /etc/init.d/`. Например, `sudo systemctl status mongodb`, для WSL будет `sudo /etc/inid.d/mongodb status`... также можно использовать `sudo service mongodb status`.

### <a name="run-your-mongo-database-in-a-local-server"></a>Запуск базы данных Mongo на локальном сервере

1. Проверьте состояние базы данных: `sudo service mongodb status` вы увидите ответ [Fail], если вы еще не начали работу с базой данных.

2. Запустите базу данных: `sudo service mongodb start` должен появиться ответ [ОК].

3. Проверьте, подключившись к серверу базы данных и выполнив команду диагностики: `mongo --eval 'db.runCommand({ connectionStatus: 1 })'`. будет выведена Текущая версия базы данных, адрес и порт сервера, а также выходные данные команды Status. Значение `1` для поля "ОК" в ответе указывает на то, что сервер работает.

4. Чтобы предотвратить запуск службы MongoDB, введите: `sudo service mongodb stop`.

> [!NOTE]
> MongoDB имеет несколько параметров по умолчанию, включая хранение данных в/Дата/дБ и выполнение на порте 27017. Кроме того, `mongod` — управляющая программа (ведущий процесс для базы данных), а `mongo` — оболочка командной строки, которая подключается к конкретному экземпляру `mongod`.

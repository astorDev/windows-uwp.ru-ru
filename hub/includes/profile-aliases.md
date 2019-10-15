---
ms.topic: include
author: mattwojo
ms.author: mattwoj
ms.date: 10/04/2019
ms.openlocfilehash: 2f7a57f1652ecab81a70c39faa1b70c42ed6a3de
ms.sourcegitcommit: 13faf9dab9946295986f8edd79b5fae0db4ed0f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72314908"
---
Ввод `sudo service mongodb start` или `sudo service postgres start` и `sudo -u postgrest psql` может оказаться утомительным.  Однако можно подумать о настройке псевдонимов в файле `.profile` на WSL, чтобы быстрее запоминать эти команды. 

Чтобы настроить собственный псевдоним или ярлык для исполнения следующих команд:

1. Откройте терминал WSL и введите `cd ~`, чтобы убедиться в том, что вы находитесь в корневом каталоге.
2. Откройте файл `.profile`, который управляет параметрами терминала, с текстовым редактором терминала, Nano: `sudo nano .profile`
3. В нижней части файла (не изменяйте параметры `# set PATH`) добавьте следующее:

    ```bash
    # My Aliases
    alias start-pg='sudo service postgresql start'
    alias run-pg='sudo -u postgres psql'
    ```

Это позволит ввести `start-pg`, чтобы запустить службу PostgreSQL, и `run-pg`, чтобы открыть оболочку psql. Можно изменить `start-pg` и `run-pg` на любое нужное имя, просто будьте внимательны, чтобы не перезаписать команду, которая уже использует postgres!

4. После добавления новых псевдонимов закройте текстовый редактор Nano, нажав **клавиши CTRL + X** — выберите `Y` (да) при появлении запроса на сохранение и ввод (без имени файла `.profile`).
5. Закройте и снова откройте терминал WSL, а затем попробуйте использовать новые команды псевдонимов.

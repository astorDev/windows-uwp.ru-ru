---
ms.topic: include
author: mattwojo
ms.author: mattwoj
ms.date: 10/04/2019
ms.openlocfilehash: 2f7a57f1652ecab81a70c39faa1b70c42ed6a3de
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72314908"
---
Вводить `sudo service mongodb start` или `sudo service postgres start` и `sudo -u postgrest psql` может быть утомительно.  Однако, вы можете рассмотреть возможность установки псевдонимов в файле `.profile` на WSL, чтобы сделать эти команды более быстрыми в использовании и легкими в запоминании. 

Настройка собственного пользовательского псевдонима или ярлыка для выполнения этих команд:

1. Откройте терминал WSL и введите `cd ~`, чтобы убедиться, что вы находитесь в корневом каталоге.
2. Откройте файл `.profile`, управляющий настройками терминала, в текстовом редакторе терминала Nano: `sudo nano .profile`.
3. В нижней части файла (не меняйте настройки `# set PATH`) добавьте следующее:

    ```bash
    # My Aliases
    alias start-pg='sudo service postgresql start'
    alias run-pg='sudo -u postgres psql'
    ```

Это позволит вам ввести `start-pg` для запуска службы postgresql и `run-pg` — для открытия оболочки psql. Вы можете изменить `start-pg` и `run-pg` на любые имена, просто следите за тем, чтобы не перезаписать команду, которую postgres уже использует!

4. После добавления новых псевдонимов выйдите из текстового редактора Nano, используя **Ctrl+X** — выберите `Y` (Да) при запросе сохранения и Enter (имя файла останется `.profile`).
5. Закройте и снова откройте терминал WSL, а затем попробуйте использовать свои новые команды ввода псевдонима.

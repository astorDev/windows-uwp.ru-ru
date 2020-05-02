---
title: Написание сценариев и автоматизация с помощью Python в Windows
description: Как приступить к работе с Python для создания сценариев, автоматизации и администрирования систем в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Python, Windows 10, Майкрософт, Системное администрирование Python, Автоматизация файлов Python, скрипты Python в Windows, Настройка Python в Windows, среда разработчика Python в Windows, среда разработки Python в Windows, Python с PowerShell, скрипты Python для задачи файловой системы
ms.localizationpriority: medium
ms.date: 07/19/2019
ms.openlocfilehash: d465d46a0524345a45dff9b1cc7c425e4cb468a4
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79208999"
---
# <a name="get-started-using-python-on-windows-for-scripting-and-automation"></a>Начало работы с Python в Windows для создания сценариев и автоматизации

Ниже приведено пошаговое руководство по настройке среды разработки и началу работы с Python для создания сценариев и автоматизации операций файловой системы в Windows.

> [!NOTE]
> В этой статье рассматривается настройка среды для использования некоторых полезных библиотек в Python, которые могут автоматизировать задачи на разных платформах, таких как поиск в файловой системе, доступ к Интернету, анализ типов файлов и т. д. с помощью подхода, ориентированного на Windows. Для операций, относящихся к Windows, извлеките [ctypes](https://docs.python.org/3/library/ctypes.html), совместимую с C библиотеку функций с кодом на другом языке программирования для Python, [winreg](https://docs.python.org/3/library/winreg.html), функции, предоставляющие API реестра Windows для Python, и [Python/WinRT](https://pypi.org/project/winrt/), включив доступ к API среды выполнения Windows в Python.

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

При использовании Python для написания скриптов, выполняющих операции файловой системы, рекомендуется [установить Python из Microsoft Store](https://www.microsoft.com/p/python-37/9nj46sx7x90p?activetab=pivot:overviewtab). При установке из Microsoft Store используется базовый интерпретатор Python3, но в дополнение к автоматическому обновлению также настраиваются параметры пути для текущего пользователя (без необходимости доступа администратора).

Если вы используете Python для **веб-разработки** в Windows, рекомендуем использовать другую установку с помощью подсистемы Windows для Linux. Ознакомьтесь с пошаговыми инструкциями в нашем руководстве: [Начало работы с Python для разработки веб-приложений в Windows](./web-frameworks.md). Если вы новичок в Python, ознакомьтесь с нашим руководством: [Get started using Python on Windows for beginners](./beginners.md) (Приступая к работе с Python в Windows для начинающих). В некоторых сложных сценариях (например, при необходимости модификации или доступа к установленным файлам Python, создания копий двоичных файлов или непосредственного использования библиотек DLL Python) может потребоваться загрузить определенный выпуск Python непосредственно с сайта [python.org](https://www.python.org/downloads/) или установить [альтернативное средство](https://www.python.org/download/alternatives), например Anaconda, Jython, PyPy, WinPython, IronPython и т. д. Мы рекомендуем это только в том случае, если вы более продвинутый программист на Python и у вас есть конкретная причина выбрать альтернативную реализацию.

## <a name="install-python"></a>Установка Python

Чтобы установить Python с помощью Microsoft Store, сделайте следующее:

1. Перейдите в меню **Пуск** (значок Windows в нижнем левом углу), введите "Microsoft Store" и щелкните ссылку, чтобы открыть магазин.

2. Когда магазин откроется, выберите **Поиск** в верхнем правом меню и введите "Python". Выберите "Python 3.7" из результатов в разделе приложений. Щелкните **Получить**.

3. После того как Python завершит процесс загрузки и установки, откройте Windows PowerShell, используя меню **Пуск** (значок Windows в нижнем левом углу). После открытия PowerShell введите `Python --version`, чтобы убедиться, что Python3 установлен на компьютере.

4. Установка Python из Microsoft Store содержит стандартный диспетчер пакетов **pip**. Pip позволяет устанавливать дополнительные пакеты, которые не входят в стандартную библиотеку Python, и управлять ими. Чтобы убедиться, что у вас есть pip, который можно использовать для установки пакетов и управления ими, введите `pip --version`.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

При использовании VS Code в качестве текстового редактора или интегрированной среды разработки (IDE) вам доступны [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) (помощь в завершении кода), [анализ кода](https://code.visualstudio.com/docs/python/linting) (помогает избежать ошибок в коде), [поддержка отладки](https://code.visualstudio.com/docs/python/debugging) (помогает находить ошибки в коде после запуска), [фрагменты кода](https://code.visualstudio.com/docs/editor/userdefinedsnippets) (шаблоны для небольших повторно используемых блоков кода) и [модульное тестирование](https://code.visualstudio.com/docs/python/unit-testing) (тестирование интерфейса кода с различными типами входных данных).

Загрузите VS Code для Windows и следуйте инструкциям по установке: [https://code.visualstudio.com](https://code.visualstudio.com).

## <a name="install-the-microsoft-python-extension"></a>Установка расширения Microsoft Python

Установите расширение Microsoft Python, чтобы воспользоваться преимуществами функций поддержки VS Code. [Подробнее.](https://code.visualstudio.com/docs/languages/python)

1. Откройте окно расширения VS Code с помощью **CTRL+SHIFT+X** (или используйте меню, чтобы перейти к **Вид** > **Расширения**).

2. В поле **Поиск расширений в Marketplace** введите:  **Python**.

3. Найдите расширение **Python (ms-python.python) от Microsoft** и нажмите зеленую кнопку **Установить**.

## <a name="open-the-integrated-powershell-terminal-in-vs-code"></a>Откройте встроенный терминал PowerShell в VS Code

VS Code содержит [встроенный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal), который позволяет открывать командную строку Python с помощью PowerShell, создавая простой рабочий процесс между редактором кода и командной строкой.

1. Откройте терминал в VS Code, выберите **Просмотр** > **Терминал** или используйте сочетание клавиш **Ctrl+`** (используя символ обратного апострофа).

    > [!NOTE]
    > По умолчанию этим терминалом должен быть PowerShell, но если его нужно изменить, используйте **Ctrl+Shift+P**, чтобы ввести команду. Введите **терминал: Выберите** Оболочку по умолчанию, и отобразится список параметров терминала, содержащий PowerShell, командную строку, WSL и т. д. Выберите ту, которую хотите использовать, и нажмите **Ctrl+Shift+`** (с помощью обратного апострофа), чтобы создать новый терминал.

2. В окне терминала VS Code откройте Python, введя: `python`

3. Попробуйте использовать интерпретатор Python, введя: `print("Hello World")`. Python вернет фразу "Hello World".

    ![Командная строка Python в VS Code](../images/python-in-vscode.png)

4. Чтобы выйти из Python, введите `exit()`, `quit()` или нажмите клавиши CTRL+Z.

## <a name="install-git-optional"></a>Установка Git (необязательно)

Если вы планируете совместно работать над кодом Python с другими пользователями или размещать проект на сайте с открытым исходным кодом (например, GitHub), примите во внимание, что VS Code поддерживает [управление версиями с помощью Git](https://code.visualstudio.com/docs/editor/versioncontrol#_git-support). Вкладка системы управления версиями в VS Code отслеживает все изменения и содержит общие команды Git (добавление, фиксация, принудительная отправка, извлечение) прямо в пользовательском интерфейсе. Сначала необходимо установить Git для включения панели управления версиями.

1. Скачайте и установите Git для Windows с [веб-сайта git-scm](https://git-scm.com/download/win).

2. В комплект входит мастер установки, который задает вам ряд вопросов о параметрах установки Git. Рекомендуется использовать все параметры по умолчанию, если у вас нет конкретной причины изменить какой-либо из них.

3. Если вы никогда не использовали Git, обратитесь к [руководствам по GitHub](https://guides.github.com/). Они помогут вам приступить к работе.

## <a name="example-script-to-display-the-structure-of-your-file-system-directory"></a>Пример сценария для вывода структуры каталога файловой системы

Распространенные задачи системного администрирования могут занимать огромное количество времени, но с помощью сценария Python вы можете их автоматизировать и не тратить на них время вовсе. Например, Python может читать содержимое файловой системы компьютера и выполнять такие операции, как вывод структуры файлов и каталогов, перемещение папок из одного каталога в другой или переименование большого количества файлов. Как правило, такие задачи могут занимать массу времени, если выполнять их вручную. Вместо этого используйте сценарий Python!

Начнем с простого сценария, в котором описано дерево каталогов и отображено структуру каталогов.

1. Откройте PowerShell, используя меню **Пуск** (нижний левый значок Windows).

2. Создайте каталог для проекта: `mkdir python-scripts`, а затем откройте этот каталог: `cd python-scripts`.

3. Создайте несколько каталогов для использования с нашим примером сценария:

    ```powershell
    mkdir food, food\fruits, food\fruits\apples, food\fruits\oranges, food\vegetables
    ```

4. Создайте несколько файлов в этих каталогах для использования с нашим сценарием:

    ```powershell
    new-item food\fruits\banana.txt, food\fruits\strawberry.txt, food\fruits\blueberry.txt, food\fruits\apples\honeycrisp.txt, food\fruits\oranges\mandarin.txt, food\vegetables\carrot.txt
    ```

5. Создайте в каталоге Python-Scripts новый файл Python:

    ```powershell
    mkdir src
    new-item src\list-directory-contents.py
    ```

6. Откройте проект в VS Code, введя: `code .`

7. Откройте окно проводника VS Code, нажав **Ctrl+Shift+E** (или используйте меню, чтобы перейти к **Вид** > **Обозреватель**) и выберите только что созданный файл list-directory-contents.py. Расширение Microsoft Python будет автоматически загружать интерпретатор Python. Загруженный интерпретатор можно увидеть в нижней части окна VS Code.

    > [!NOTE]
    > Python — интерпретируемый язык, то есть он выступает в качестве виртуальной машины, имитируя физический компьютер. Существуют различные типы интерпретаторов Python, которые можно использовать: Python 2, Python 3, Anaconda, PyPy и т. д. Чтобы выполнить код Python и получить Python IntelliSense, необходимо указать интерпретатор, который следует использовать в VS Code. Если нет конкретной причины для выбора другого интерпретатора, мы рекомендуем придерживаться интерпретатора, который VS Code выбирает по умолчанию (Python 3 в нашем случае). Чтобы изменить интерпретатор Python, выберите интерпретатор, который сейчас отображается в синей панели в нижней части окна VS Code, или откройте **палитру команд** (Ctrl+Shift+P) и введите команду **Python: Select Interpreter** (Python: выбор интерпретатора). На экране появится список установленных интерпретаторов Python. [Using Python environments in VS Code](https://code.visualstudio.com/docs/python/environments)(Использование сред Python в VS Code).

    ![Выбор интерпретатора Python в VS Code](../images/interpreterselection.gif)

8. Вставьте следующий код в файл list-directory-contents.py, а затем выберите **Сохранить**:

    ```python
    import os

    root = os.path.join('..', 'food')
    for directory, subdir_list, file_list in os.walk(root):
        print('Directory:', directory)
        for name in subdir_list:
            print('Subdirectory:', name)
        for name in file_list:
            print('File:', name)
        print()
    ```

9. Откройте интегрированный терминал VS Code (**Ctrl+`** с помощью символа обратного апострофа) и введите каталог src, в котором вы только что сохранили сценарий Python:

    ```powershell
    cd src
    ```

10. Запустите сценарий в PowerShell с помощью:

    ```powershell
    python3 .\list-directory-contents.py
    ```

    Результат должен выглядеть примерно так:

    ```powershell
    Directory: ..\food
    Subdirectory: fruits
    Subdirectory: vegetables

    Directory: ..\food\fruits
    Subdirectory: apples
    Subdirectory: oranges
    File: banana.txt
    File: blueberry.txt
    File: strawberry.txt

    Directory: ..\food\fruits\apples
    File: honeycrisp.txt

    Directory: ..\food\fruits\oranges
    File: mandarin.txt

    Directory: ..\food\vegetables
    File: carrot.txt
    ```

11. Используйте Python, чтобы вывести выходные данные каталога файловой системы в собственный текстовый файл, введя следующую команду непосредственно в терминале PowerShell: `python3 list-directory-contents.py > food-directory.txt`

Поздравляем! Вы только что написали автоматизированный сценарий системного администрирования, который считывает созданные вами каталог и файлы и использует Python для отображения, а затем для вывода структуры каталога в собственный текстовый файл.

## <a name="example-script-to-modify-all-files-in-a-directory"></a>Пример сценария для изменения всех файлов в каталоге

В этом примере используются только что созданные файлы и каталоги, каждый из которых следует переименовать путем добавления даты последнего изменения файла в начало имени файла.

1. В папке **src** в каталоге **python-scripts** создайте новый файл Python для своего сценария:

    ```powershell
    new-item update-filenames.py
    ```

2. Откройте файл update-filenames.py, вставьте следующий код в файл и сохраните его:

    > [!NOTE]
    > os.getmtime возвращает метку времени в тактах, что трудно читать. Сначала его необходимо преобразовать в стандартную строку datetime.

    ```python
    import datetime
    import os

    root = os.path.join('..', 'food')
    for directory, subdir_list, file_list in os.walk(root):
        for name in file_list:
            source_name = os.path.join(directory, name)
            timestamp = os.path.getmtime(source_name)
            modified_date = str(datetime.datetime.fromtimestamp(timestamp)).replace(':', '.')
            target_name = os.path.join(directory, f'{modified_date}_{name}')

            print(f'Renaming: {source_name} to: {target_name}')

            os.rename(source_name, target_name)
    ```

3. Протестируйте сценарий update-filenames.py, запустив его: `python3 update-filenames.py` а затем снова запустите сценарий list-directory-contents.py: `python3 list-directory-contents.py`

4. Результат должен выглядеть примерно так:

    ```powershell
    Renaming: ..\food\fruits\banana.txt to: ..\food\fruits\2019-07-18 12.24.46.385185_banana.txt
    Renaming: ..\food\fruits\blueberry.txt to: ..\food\fruits\2019-07-18 12.24.46.391170_blueberry.txt
    Renaming: ..\food\fruits\strawberry.txt to: ..\food\fruits\2019-07-18 12.24.46.389174_strawberry.txt
    Renaming: ..\food\fruits\apples\honeycrisp.txt to: ..\food\fruits\apples\2019-07-18 12.24.46.395160_honeycrisp.txt
    Renaming: ..\food\fruits\oranges\mandarin.txt to: ..\food\fruits\oranges\2019-07-18 12.24.46.398151_mandarin.txt
    Renaming: ..\food\vegetables\carrot.txt to: ..\food\vegetables\2019-07-18 12.24.46.402496_carrot.txt

    PS C:\src\python-scripting\src> python3 .\list-directory-contents.py
    ..\food\
    Directory: ..\food
    Subdirectory: fruits
    Subdirectory: vegetables

    Directory: ..\food\fruits
    Subdirectory: apples
    Subdirectory: oranges
    File: 2019-07-18 12.24.46.385185_banana.txt
    File: 2019-07-18 12.24.46.389174_strawberry.txt
    File: 2019-07-18 12.24.46.391170_blueberry.txt

    Directory: ..\food\fruits\apples
    File: 2019-07-18 12.24.46.395160_honeycrisp.txt

    Directory: ..\food\fruits\oranges
    File: 2019-07-18 12.24.46.398151_mandarin.txt

    Directory: ..\food\vegetables
    File: 2019-07-18 12.24.46.402496_carrot.txt

    ```

5. Используйте Python для вывода новых имен каталогов файловой системы с меткой времени последнего изменения в начале текстового файла, введя эту команду непосредственно в терминале PowerShell: `python3 list-directory-contents.py > food-directory-last-modified.txt`

Надеемся, что вы узнали несколько интересных вещей об использовании сценариев Python для автоматизации основных задач системного администрирования. Конечно, есть еще масса информации, но мы надеемся, что это позволит вам начать работу с нужным нижним колонтитулом. Ниже мы предоставили несколько дополнительных ресурсов, чтобы вы продолжили обучение.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Документы Python: Доступ к файлам и каталогам](https://docs.python.org/3.7/library/filesys.html): Документация по Python, посвященная работе с файловыми системами и использованию модулей для чтения свойств файлов, управления путями в переносимом виде и создания временных файлов.
- [Изучение Python: Учебник String_Formatting](https://www.learnpython.org/en/String_Formatting): Дополнительные сведения об использовании оператора "%" для форматирования строк.
- [10 Python File System Methods You Should Know](https://towardsdatascience.com/10-python-file-system-methods-you-should-know-799f90ef13c2)(10 методов файловой системы Python с которыми вы должны быть знакомы): Средняя статья об управлении файлами и папками с помощью `os` и `shutil`.
- [The Hitchhikers Guide to Python (Попутное руководство по Python). Администрирование систем](https://docs.python-guide.org/scenarios/admin/). Пособие "с собственным мнением", предлагающее обзоры и лучшие рекомендации по темам, связанным с Python. В этом разделе описываются средства и платформы администрирования систем. Данное руководство размещено на сервере GitHub, чтобы вы могли подавать заявки и вносить свой вклад.

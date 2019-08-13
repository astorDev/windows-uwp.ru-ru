---
title: Написание сценариев и автоматизация с помощью Python в Windows
description: Как приступить к работе с Python для создания сценариев, автоматизации и администрирования систем в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Python, Windows 10, Майкрософт, Системное администрирование Python, Автоматизация файлов Python, скрипты Python в Windows, Настройка Python в Windows, среда разработчика Python в Windows, среда разработки Python в Windows, Python с PowerShell, скрипты Python для задачи файловой системы
ms.localizationpriority: medium
ms.date: 07/19/2019
ms.openlocfilehash: 93fdea3347cc15aa6231ff90fb18eb2f7defb201
ms.sourcegitcommit: a28a32fff9d15ecf4a9d172cd0a04f4d993f9d76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959071"
---
# <a name="get-started-using-python-on-windows-for-scripting-and-automation"></a>Приступая к работе с Python в Windows для написания сценариев и автоматизации

Ниже приведено пошаговое руководство по настройке среды разработки и началу работы с Python для создания сценариев и автоматизации операций файловой системы в Windows.

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

При использовании Python для написания скриптов, выполняющих операции файловой системы, рекомендуется [установить Python из Microsoft Store](https://www.microsoft.com/en-us/p/python-37/9nj46sx7x90p?activetab=pivot:overviewtab). Установка с помощью Microsoft Store использует базовый интерпретатор Python3, но обрабатывает настройки параметров пути для текущего пользователя (без необходимости доступа администратора) в дополнение к автоматическому обновлению.

Если вы используете Python для **разработки веб-приложений** в Windows, рекомендуем использовать другую программу установки, используя подсистему Windows для Linux. Ознакомьтесь с пошаговым руководством в нашем руководстве: Приступите [к работе с Python для разработки веб-приложений в Windows](./python-for-web.md). Если вы новичок в Python, ознакомьтесь с нашим руководством: Начните [использовать Python в Windows для начинающих](./python-for-education.md). В некоторых сложных сценариях (например, при необходимости доступа к установленным файлам Python, создания копий двоичных файлов или непосредственного использования библиотек DLL Python) может потребоваться загрузить определенный выпуск Python непосредственно из [Python.org](https://www.python.org/downloads/) или установить [альтернатива](https://www.python.org/download/alternatives), например Anaconda, Jython, PyPy, Винписон, IronPython и т. д. Это рекомендуется только в том случае, если вы являетесь более сложным программистом Python с определенной причиной выбора альтернативной реализации.

## <a name="install-python"></a>Установка Python

Чтобы установить Python с помощью Microsoft Store, выполните следующие действия.

1. Перейдите в меню " **Пуск** " (нижний левый значок Windows) и введите "Microsoft Store", чтобы открыть магазин.

2. После открытия хранилища выберите в верхнем правом меню **Поиск** и введите Python. Откройте Python 3,7 из результатов в разделе приложения. Выберите **получить**.

3. После того как Python завершит процесс загрузки и установки, откройте Windows PowerShell с помощью меню " **Пуск** " (нижний левый значок Windows). После открытия PowerShell введите `Python --version` , чтобы убедиться, что Python3 установлен на компьютере.

4. Microsoft Storeная установка Python включает **PIP**, Стандартный диспетчер пакетов. PIP позволяет устанавливать и управлять дополнительными пакетами, которые не входят в стандартную библиотеку Python. Чтобы убедиться, что у вас также есть доступ к PIP для установки пакетов и `pip --version`управления ими, введите.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Используя VS Code в качестве текстового редактора или интегрированной среды разработки (IDE), можно воспользоваться преимуществами [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) (Справка по завершению кода), [linting](https://code.visualstudio.com/docs/python/linting) (помогает избежать ошибок в коде), [поддержки отладки](https://code.visualstudio.com/docs/python/debugging) (помогает обнаружить ошибки в код после его запуска), фрагменты [кода](https://code.visualstudio.com/docs/editor/userdefinedsnippets) (шаблоны для небольших повторно используемых блоков кода) и [модульное тестирование](https://code.visualstudio.com/docs/python/unit-testing) (тестирование интерфейса кода с различными типами входных данных).

Скачайте VS Code для Windows и следуйте инструкциям по установке: [https://code.visualstudio.com](https://code.visualstudio.com).

## <a name="install-the-microsoft-python-extension"></a>Установка расширения Microsoft Python

Чтобы воспользоваться преимуществами функций поддержки VS Code, необходимо установить расширение Microsoft Python. [Подробнее](https://code.visualstudio.com/docs/languages/python).

1. Откройте окно расширения VS Code, нажав **клавиши CTRL + SHIFT + X** (или с помощью меню перейдите к пункту **Просмотр** > **расширений**).

2. В поле лучшие **расширения поиска в Marketplace** введите:  **Python**.

3. Найдите расширение **Python (MS-Python. Python) по** расширению Майкрософт и нажмите зеленую кнопку " **установить** ".

## <a name="open-the-integrated-powershell-terminal-in-vs-code"></a>Откройте встроенный терминал PowerShell в VS Code

VS Code содержит [встроенный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) , позволяющий открывать командную строку Python с помощью PowerShell, создавая простой рабочий процесс между редактором кода и командной строкой.

1. Откройте терминал в VS Code, выберите **Просмотреть** > **терминал**или используйте сочетание **клавиш CTRL + '** (с помощью символа обратной кавычки).

    > [!NOTE]
    > Терминал по умолчанию должен быть PowerShell, но если его нужно изменить, используйте **сочетание клавиш CTRL + SHIFT + P** , чтобы ввести команду паллетте. Введите **терминал: Выберите оболочка** по умолчанию и отобразится список параметров терминала, содержащий PowerShell, командную строку, WSL и т. д. Выберите ту, которую хотите использовать, и **нажмите клавиши Ctrl + Shift + '** (с помощью обратного импульса), чтобы создать новый терминал.

2. В окне терминала VS Code откройте Python, введя:`python`

3. Попробуйте интерпретатор Python, введя: `print("Hello World")`. Python возвратит инструкцию "Hello World".

    ![Командная строка Python в VS Code](../../images/python-in-vscode.png)

4. Чтобы выйти из Python, можно ввести `exit()`, `quit()`или нажать клавиши CTRL + Z.

## <a name="install-git-optional"></a>Установка Git (необязательно)

Если планируется совместная работа с другими пользователями в коде Python или размещение проекта на сайте с открытым исходным кодом (например, GitHub), VS Code поддерживает [Управление версиями с помощью Git](https://code.visualstudio.com/docs/editor/versioncontrol#_git-support). Вкладка система управления версиями в VS Code отслеживает все изменения и содержит общие команды git (Добавление, фиксация, принудительная отправка, извлечение), встроенные прямо в пользовательский интерфейс. Сначала необходимо установить Git для включения панели управления исходным кодом.

1. Скачайте и установите Git для Windows с [веб-сайта Git-SCM](https://git-scm.com/download/win).

2. В состав входит мастер установки, который запрашивает у вас ряд вопросов о параметрах установки Git. Рекомендуется использовать все параметры по умолчанию, если у вас нет конкретной причины для изменения чего-либо.

3. Если вы еще не работали с Git, [руководства по GitHub](https://guides.github.com/) помогут вам приступить к работе.

## <a name="example-script-to-display-the-structure-of-your-file-system-directory"></a>Пример скрипта для вывода структуры каталога файловой системы

Распространенные задачи системного администрирования могут занимать огромное количество времени, но с помощью сценария Python можно автоматизировать эти задачи, чтобы они не занимали времени. Например, Python может читать содержимое файловой системы компьютера и выполнять такие операции, как печать структуры файлов и каталогов, перемещение папок из одного каталога в другой или переименование сотен файлов. Как правило, такие задачи могут занимать массу времени, если их придется выполнять вручную. Вместо этого используйте скрипт Python!

Начнем с простого сценария, который проходит по дереву каталогов и отображает структуру каталогов.

1. Откройте PowerShell, используя меню " **Пуск** " (нижний левый значок Windows).

2. Создайте каталог для проекта: `mkdir python-scripts`, а затем откройте этот каталог:. `cd python-scripts`

3. Создайте несколько каталогов для использования с нашим примером сценария:

    ```powershell
    mkdir food, food/fruits, food/fruits/apples, food/fruits/oranges, food/vegetables
    ```

4. Создайте несколько файлов в этих каталогах для использования с нашим сценарием:

    ```powershell
    new-item food/fruits/banana.txt, food/fruits/strawberry.txt, food/fruits/blueberry.txt, food/fruits/apples/honeycrisp.txt, food/fruits/oranges/mandarin.txt, food/vegetables/carrot.txt
    ```

5. Создайте в каталоге Python-Scripts новый файл Python:

    ```powershell
    mkdir src
    new-item src/list-directory-contents.py
    ```

6. Откройте проект в VS Code, введя:`code .`

7. Откройте VS Code окно проводника, нажмите **клавиши CTRL + SHIFT + E** (или используйте меню для перехода к обозревателю **представлений** > ) и выберите только что созданный файл list-Directory-Contents.py. Расширение Microsoft Python будет автоматически загружать интерпретатор Python. Вы можете увидеть, какой интерпретатор был загружен в нижней части окна VS Code.

    > [!NOTE]
    > Python — интерпретируемый язык, то есть он выступает в качестве виртуальной машины, имитируя физический компьютер. Существуют различные типы интерпретаторов Python, которые можно использовать: Python 2, Python 3, Anaconda, PyPy и т. д. Чтобы выполнить код Python и получить IntelliSense для Python, необходимо указать VS Code, какой интерпретатор использовать. Рекомендуется придерживаться интерпретатора, который VS Code выбирается по умолчанию (Python 3 в нашем случае), если нет конкретной причины для выбора другого. Чтобы изменить интерпретатор Python, выберите интерпретатор, который сейчас отображается в синей строке в нижней части окна VS Code, или откройте **палитру команд** (Ctrl + Shift + P) и введите команду **Python: Выберите интерпретатор**. Отобразится список установленных на данный момент интерпретаторов Python. Дополнительные [сведения о настройке окружений Python](https://code.visualstudio.com/docs/python/environments).

    ![Выбор интерпретатора Python в VS Code](../../images/interpreterselection.gif)

8. Вставьте следующий код в файл list-directory-contents.py и нажмите кнопку Save ( **сохранить**):

    ```python
    import os

    root = '%s%s%s' % ('..', os.path.sep, 'food')
    for directory, subdir_list, file_list in os.walk(root):
        print('Directory: ' + directory)
        for name in subdir_list:
            print ('Subdirectory: ' + name)
        for name in file_list:
            print('File: ' + name)
        print(os.linesep)
    ```

9. Откройте VS Code интегрированный терминал (**CTRL + '** , используя символ обратной кавычки) и введите каталог src, в котором только что сохранен сценарий Python:

    ```powershell
    cd src
    ```

10. Запустите скрипт в PowerShell с помощью:

    ```powershell
    python3 .\list-directory-contents.py
    ```

    Вы должны увидеть выходные данные следующего вида:

    ```powershell
    Directory: ../food
    Subdirectory: fruits
    Subdirectory: vegetables

    Directory: ../food\fruits
    Subdirectory: apples
    Subdirectory: oranges
    File: banana.txt
    File: blueberry.txt
    File: strawberry.txt

    Directory: ../food\fruits\apples
    File: honeycrisp.txt

    Directory: ../food\fruits\oranges
    File: mandarin.txt

    Directory: ../food\vegetables
    File: carrot.txt
    ```

11. Используйте Python, чтобы вывести выходные данные каталога файловой системы в собственный текстовый файл, введя следующую команду непосредственно в терминале PowerShell:`python3 list-directory-contents.py > food-directory.txt`

Поздравляем! Вы только что написали автоматизированный сценарий системного администрирования, который считывает созданный вами каталог и файлы и использует Python для отображения, а затем для вывода структуры каталогов в собственный текстовый файл.

## <a name="example-script-to-modify-all-files-in-a-directory"></a>Пример скрипта для изменения всех файлов в каталоге

В этом примере используются только что созданные файлы и каталоги с переименованием каждого из файлов путем добавления даты последнего изменения файла в начало имени файла.

1. В папке **src** в каталоге **Python-Scripts** создайте новый файл Python для своего скрипта:

    ```powershell
    new-item update-filenames.py
    ```

2. Откройте файл update-filenames.py, вставьте следующий код в файл и сохраните его:

    > [!NOTE]
    > операционная система. жетмтиме возвращает метку времени в тактах, что нелегко читается. Сначала его необходимо преобразовать в стандартную строку DateTime.

    ```python
    import datetime
    import os

    root = '%s%s%s' % ('..', os.path.sep, 'food')
    for directory, subdir_list, file_list in os.walk(root):
        for name in file_list:
            source_name = '%s%s%s' % (directory, os.path.sep, name)
            timestamp = os.path.getmtime(source_name)
            modified_date = str(datetime.datetime.fromtimestamp(timestamp)).replace(':', '.')
            target_name = '%s%s%s_%s' % (directory, os.path.sep, modified_date, name)

            print ('Renaming: %s to: %s' % (source_name, target_name))

            os.rename(source_name, target_name)
    ```

3. Протестируйте сценарий Update-filenames.py, запустив его `python3 update-filenames.py` , а затем снова запустите сценарий List-Directory-Contents.py:`python3 list-directory-contents.py`

4. Вы должны увидеть выходные данные следующего вида:

    ```powershell
    Renaming: ..\food\fruits\banana.txt to: ..\food\fruits\2019-07-18 12.24.46.385185_banana.txt
    Renaming: ..\food\fruits\blueberry.txt to: ..\food\fruits\2019-07-18 12.24.46.391170_blueberry.txt
    Renaming: ..\food\fruits\strawberry.txt to: ..\food\fruits\2019-07-18 12.24.46.389174_strawberry.txt
    Renaming: ..\food\fruits\apples\honeycrisp.txt to: ..\food\fruits\apples\2019-07-18 12.24.46.395160_honeycrisp.txt
    Renaming: ..\food\fruits\oranges\mandarin.txt to: ..\food\fruits\oranges\2019-07-18 12.24.46.398151_mandarin.txt
    Renaming: ..\food\vegetables\carrot.txt to: ..\food\vegetables\2019-07-18 12.24.46.402496_carrot.txt

    ~/src/python-scripting/src$ python3 .\list-directory-contents.py
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

5. Используйте Python для вывода новых имен каталогов файловой системы с меткой времени последнего изменения в начале текстового файла, введя эту команду непосредственно в терминале PowerShell:`python3 list-directory-contents.py > food-directory-last-modified.txt`

Надеемся, что вы узнали несколько интересных вещей об использовании скриптов Python для автоматизации основных задач системного администрирования. Конечно, есть масса информации, но мы надеемся, что это позволит вам начать работу с нужным нижним колонтитулом. Мы предоставили несколько дополнительных ресурсов, чтобы продолжить обучение ниже.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Документы Python: Доступ к](https://docs.python.org/3.7/library/filesys.html)файлам и каталогам: Документация по Python, посвященная работе с файловыми системами и использованию модулей для чтения свойств файлов, управления путями в переносимом виде и создания временных файлов.
- [Изучение Python: Учебник](https://www.learnpython.org/en/String_Formatting)по String_Formatting: Дополнительные сведения об использовании оператора "%" для форматирования строк.
- [10 методов файловой системы Python, которые следует учитывать](https://towardsdatascience.com/10-python-file-system-methods-you-should-know-799f90ef13c2): Средняя статья об управлении файлами и папками с `os` помощью `shutil`и.
- [Руководство по Хитчхикерс к Python: Администрирование](https://docs.python-guide.org/scenarios/admin/)систем: «Упрямого Guide», предлагающий обзоры и рекомендации по темам, связанным с Python. В этом разделе описываются средства и платформы системного администратора. Это пошаговое руководством размещено на сайте GitHub, поэтому вы можете вносить в него проблемы с файлами.

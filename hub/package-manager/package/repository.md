---
title: Отправка манифеста в репозиторий
description: ''
author: denelon
ms.author: denelon
ms.date: 04/29/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: c5ebcc564b4db16c1d16385cbeaf7fd6d82c8f18
ms.sourcegitcommit: 8193aef04deb3514eb2d34bfe5cb9424ba12cd76
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83865031"
---
# <a name="submit-your-manifest-to-the-repository"></a>Отправка манифеста в репозиторий

[!INCLUDE [preview-note](../../includes/package-manager-preview.md)]

После создания [манифеста пакета](manifest.md), который описывает приложение, вы можете отправить манифест в репозиторий Диспетчера пакетов Windows. Это общедоступный репозиторий, содержащий коллекцию манифестов, к которым может получить доступ программа **winget**. Чтобы отправить манифест, передайте его в репозиторий с открытым исходным кодом [https://github.com/microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs) на сайте GitHub.

После отправки запроса на включение нового манифеста в репозиторий GitHub автоматизированный процесс проверит ваш файл манифеста и убедится, что пакет не вредоносный. Если проверка прошла успешно, пакет будет добавлен в общедоступный репозиторий Диспетчера пакетов Windows, чтобы его можно было обнаружить с помощью клиентской программы **winget**. Обратите внимание на различие между манифестами в репозитории GitHub с открытым исходным кодом и в общедоступном репозитории Диспетчера пакетов Windows.

> [!IMPORTANT]
> Корпорация Майкрософт оставляет за собой право отказаться от отправки по любой причине.

## <a name="third-party-repositories"></a>Репозитории сторонних разработчиков

На данный момент не известно о каких-либо репозиториях сторонних разработчиков. Корпорация Майкрософт вместе с несколькими партнерами разрабатывает протоколы или API для обеспечения поддержки репозиториев сторонних разработчиков.

## <a name="manifest-validation"></a>Проверка манифеста

При отправке манифеста в репозиторий [https://github.com/microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs) на сайте GitHub манифест будет автоматически проверен и оцениваться для обеспечения безопасности экосистемы Windows. Манифесты также могут быть просмотрены вручную.

## <a name="how-to-submit-your-manifest"></a>Отправка манифеста

Чтобы отправить манифест в репозиторий, выполните следующие действия.

### <a name="step-1-validate-your-manifest"></a>Шаг 1. Проверка манифеста

В программе **winget** предусмотрена команда [validate](..\winget\validate.md), позволяющая убедиться в правильности созданного манифеста. Используйте эту команду, чтобы проверить манифест.

```CMD
winget validate \<manifest-file>
```

Если проверка завершается сбоем, воспользуйтесь сообщениями об ошибках, чтобы узнать номер строки и внести исправление. После проверки манифеста его можно отправить в репозиторий.

### <a name="step-2-clone-the-repository"></a>Шаг 2. Клонируйте репозиторий.

Затем создайте вилку репозитория и клонируйте ее.

1. Перейдите на страницу [https://github.com/microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs) в браузере и щелкните **Вилка**.
    ![Изображение вилки](images\fork.png)

2. Чтобы клонировать вилку из среды командной строки, например командной строки Windows или PowerShell, используйте следующую команду.
    ```CMD
    git clone \<your-fork-name>
    ```

 3. В случае нескольких отправок вместо вилки создайте ветвь. Сейчас поддерживается отправка только одного файла манифеста за раз.
    ```CMD
    git checkout -b \<branch-name>
    ```

### <a name="step-3-add-your-manifest-to-the-local-repository"></a>Шаг 3. Добавление манифеста в локальный репозиторий

Файл манифеста необходимо добавить в репозиторий с использованием следующей структуры папок:

**manifests** / **издатель** / **приложение** / **версия.yaml**

* Папка **manifests** является корневой для всех манифестов в репозитории.
* Имя папки **издатель** — это название компании, опубликовавшей программное обеспечение. Например, **Microsoft**.
* Имя папки **приложение** — это имя приложения или средства. Например, **VSCode**.
* **версия.YAML** — это имя файла манифеста. В качестве имени файла необходимо указать текущую версию приложения. Например, **1.0.0.yaml**.

>[!IMPORTANT]
> Значение `Id` в манифесте должно соответствовать названию издателя и имени приложения, используемым в пути к папке манифеста, а значение `version` должно совпадать с версией в имени файла. Дополнительные сведения см. в статье [Создание манифеста пакета](manifest.md#tips-and-best-practices).

### <a name="step-4-submit-your-manifest-to-the-remote-repository"></a>Шаг 4. Отправка манифеста в удаленный репозиторий

Теперь вы можете отправить новый манифест в удаленный репозиторий.

1. Для подготовки к отправке используйте команду `add`.
    ```CMD
    git add manifests\Contoso\ContosoApp\1.0.0.yaml
    ```

2. чтобы зафиксировать изменения и предоставить сведения об отправке, используйте команду `commit`.
    ```CMD
    git commit -m "Submitting  ContosoApp version 1.0.0.yaml"
    ```

3. Чтобы отправить изменения в удаленный репозиторий, используйте команду `push`.
    ```CMD
    git push
    ```

### <a name="step-5-create-a-pull-request"></a>Шаг 5. Создание запроса на включение внесенных изменений

После отправки изменений вернитесь на страницу [https://github.com/microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs) и создайте запрос на включение внесенных изменений для слияния вилки или ветви с **главной** ветвью.

![Изображение вкладки запроса на включение внесенных изменений](images\pull-request.png)

## <a name="validation-process"></a>Процесс проверки

При создании запроса на включение внесенных изменений запускается процесс автоматизации, который проверяет манифест и обрабатывает запрос. Мы добавляем метки к запросу на вытягивание, чтобы вы могли отслеживать ход его выполнения.

### <a name="submission-expectations"></a>Ожидаемые характеристики отправки

Все отправки приложений в репозиторий Диспетчера пакетов Windows должны осуществляться надлежащим образом. Ниже приведены некоторые из ожидаемых характеристик отправки.

* Манифест соответствует [требованиям схемы](manifest.md#manifest-contents).
* Все URL-адреса в манифесте ведут к надежным веб-сайтам.
* Установщик и приложение не содержат вирусов.
* Корректно установить и удалить приложение могут как администраторы, так и другие пользователи.
* Установщик поддерживает неинтерактивные режимы.
* Все записи манифеста точны и не вводят в заблуждение.
* Установщик поставляется непосредственно с веб-сайта издателя.

### <a name="pull-request-labels"></a>Метки запроса на включение внесенных изменений

Во время проверки мы применяем к запросу на включение внесенных изменений ряд меток, которые позволяют сообщать о ходе его выполнения.

* **Needs: author feedback**. Произошел сбой отправки. Мы повторно назначим вам запрос на включение внесенных изменений. Если вы не устраните эту проблему в течение 10 дней, этот запрос будет закрыт.
* **Manifest-Validation-Error**. Отправленный манифест содержит синтаксическую ошибку.
* **URL-Validation-Error**. Один или несколько URL-адресов в отправке не прошли проверку [SmartScreen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-smartscreen/microsoft-defender-smartscreen-overview).
* **Binary-Validation-Error**. Отправленный установщик приложения не прошел проверку на вирусы или возникло несоответствие хэша.
* **Pull-Request-Error**: Возникла проблема с запросом на включение внесенных изменений. Например, структура папок не соответствует [требуемому формату](#step-3-add-your-manifest-to-the-local-repository).
* **Validation-Error**. Отправленное приложение не прошло общий проверочный тест.
* **Validation-Installation-Error**. Отправленное приложение не прошло тестирование установки.
* **Validation-Uninstall-Error**. Отправленное приложение не прошло тестирование удаления.
* **Validation-Virus-Scan-Error**. Отправленное приложение не прошло проверку на наличие вирусов.
* **Azure-Pipeline-Passed**. Манифест прошел первую часть проверки. После выполнения этого шага ваш запрос на включение внесенных изменений будет назначен нашей команде тестирования для окончательной проверки.
* **Validation-Completed**: Проверка завершена, и ваш запрос на включение внесенных изменений будет выполнен.
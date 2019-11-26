---
title: Получение примеров приложений UWP
description: Узнайте, как скачать примеры кода UWP на сайте GitHub.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, sample code, code samples
ms.assetid: 393c5a81-ee14-45e7-acd7-495e5d916909
ms.localizationpriority: medium
ms.openlocfilehash: ac3c99bc364e81386a362f1d1b5530bee9d462c4
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259517"
---
# <a name="get-uwp-app-samples"></a>Получение примеров приложений UWP

Примеры приложений универсальной платформы Windows (UWP) доступны в репозиториях на GitHub. Чтобы воспользоваться упорядоченным по категориям списком с возможностью поиска, см. раздел с [примерами](https://developer.microsoft.com/windows/samples). Можете также обратиться к репозиторию [Microsoft/Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples "Репозиторий GitHub с примерами приложений универсальной платформы Windows"). Этот репозиторий содержит примеры, демонстрирующие все функции UWP и шаблоны использования их API.

![Репозиторий примеров UWP на GitHub](images/GitHubUWPSamplesPage.png)

## <a name="download-the-code"></a>Скачать код

Чтобы загрузить образцы, перейдите в [репозиторий](https://github.com/Microsoft/Windows-universal-samples "Репозиторий GitHub с примерами приложений универсальной платформы Windows"). Выберите **Clone or download** (Клонировать или скачать), а затем — **Download ZIP** (Скачать ZIP-файл). 

![Скачивание примеров](images/SamplesDownloadButton.png)

Вы также можете [загрузить образцы](https://github.com/Microsoft/Windows-universal-samples/archive/master.zip "Скачать ZIP-файл с примерами приложений универсальной платформы Windows") из этой статьи.

В ZIP-файле для скачивания всегда содержатся последние версии примеров. Для скачивания файла не требуется учетная запись GitHub. Если выпущено обновление SDK или необходимо получить только последние изменения и добавления, просто скачайте актуальный ZIP-файл.

> [!NOTE]
> Для открытия, создания и запуска примеров UWP требуется Visual Studio 2015 или более поздней версии, а также пакет Windows SDK. Вы можете получить [бесплатную копию Visual Studio Community](https://www.microsoft.com/?ref=go). Visual Studio Community поддерживает создание приложений UWP.  
>
> Для правильной работы примеров нужно распаковать весь архив, а не только отдельные примеры. Все примеры зависят от папки SharedContent в архиве. Примеры функций UWP используют связанные файлы в Visual Studio, чтобы уменьшить дублирование общих файлов, включая файлы шаблонов примеров и ресурсы изображений. Общие файлы хранятся в папке SharedContent в корне репозитория. В файлах проекта для обращения к общим файлам используются ссылки.
> 

## <a name="open-the-samples"></a>Открытие примеров

Скачайте ZIP-файл и откройте примеры в Visual Studio.

1.  Прежде чем распаковывать архив, щелкните файл правой кнопкой мыши и выберите **Свойства** > **Разблокировать** > **Применить**. Затем распакуйте архив в локальную папку на компьютере.

    ![Распакованный архив](images/SamplesUnzip1.png)
2.  Каждая папка в папке Samples содержит пример функции UWP.

    ![Примеры папок](images/SamplesUnzip2.png)
3.  Выберите пример, скажем, Altimete. Вложенные папки указывают на поддерживаемые языки.

    ![Языковые папки](images/SamplesUnzip3.png)
4.  Выберите папку для языка, который нужно использовать. В содержимом папки вы увидите файл решения Visual Studio (с расширением .sln), который можно открыть в Visual Studio. Например, *Altimeter.sln*.

    ![Решение VS](images/SamplesUnzip4.png)

## <a name="give-feedback-ask-questions-and-report-issues"></a>Обратная связь, вопросы и сообщения о проблемах

Если у вас возникают проблемы или вопросы, перейдите на вкладку **Issues** (Проблемы) репозитория, чтобы создать запрос. Мы сделаем все возможное, чтобы помочь вам.

![Изображение раздела обратной связи](images/GitHubUWPSamplesFeedback.png)

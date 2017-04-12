---
title: "Получение примеров универсальной платформы Windows (UWP) из GitHub"
description: "Узнайте, как скачать примеры функций UWP с GitHub"
author: JoshuaPartlow
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 393c5a81-ee14-45e7-acd7-495e5d916909
ms.openlocfilehash: e576150cd06000405fa4754e147fbc1546cdf703
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
#<a name="get-the-universal-windows-platform-uwp-samples-from-github"></a>Получение примеров универсальной платформы Windows (UWP) из GitHub
Примеры приложений UWP доступны в репозиториях на GitHub. Если вы работаете с UWP, впервые, имеет смысл начать с репозитория [Microsoft/Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples "Universal Windows Platform app samples GitHub repository"), где вы найдете примеры всех функций UWP и шаблоны использования соответствующих API.  
![Репозиторий примеров UWP в GitHub](images/GitHubUWPSamplesPage.png) Дополнительные примеры доступны в разделе [Примеры](https://developer.microsoft.com/windows/samples "Dev Center samples") Центра разработки.  

##<a name="download-the-code"></a>Скачать код
Чтобы скачать примеры, перейдите в [репозиторий](https://github.com/Microsoft/Windows-universal-samples "Репозиторий примеров приложений универсальной платформы Windows на GitHub") и выберите **Клонировать или скачать**, а затем— **Скачать ZIP-файл**. Или просто нажмите [здесь](https://github.com/Microsoft/Windows-universal-samples/archive/master.zip "Скачать ZIP-файл с примерами приложений универсальной платформы Windows").

В этом ZIP-файле вы всегда найдете актуальные примеры. Учетная запись GitHub для скачивания не требуется. Если выпущено обновление SDK или необходимо получить только последние изменения и добавления, просто скачайте актуальный ZIP-файл.

![Скачивание примера](images/SamplesDownloadButton.png)


> **Примечание**: для открытия, создания и запуска примеров UWP требуется Visual Studio 2015 и пакет Windows SDK. Если среда Visual Studio еще не установлена, бесплатный экземпляр Visual Studio 2015 Community Edition с поддержкой создания приложений UWP можно получить [здесь](http://go.microsoft.com/fwlink/p/?LinkID=280676 "Скачать средства разработки для Windows").  
>
> Кроме того, не забудьте распаковать весь архив, а не только отдельные примеры. Все примеры зависят от папки SharedContent в архиве. Примеры функций UWP используют связанные файлы в Visual Studio, чтобы уменьшить дублирование общих файлов, включая файлы шаблонов примеров и ресурсы изображений. Эти общие файлы хранятся в папке SharedContent в корневом каталоге репозитория, а файлы проекта ссылаются на них с помощью ссылок.

Скачайте ZIP-файл и откройте примеры в Visual Studio:

1.  Прежде чем распаковывать архив, щелкните его правой кнопкой мыши и выберите **Свойства** > **Разблокировать** > **Применить**. Затем распакуйте архив в локальную папку на компьютере.

    ![Распакованный архив](images/SamplesUnzip1.png)
2.  В папке примеров вы увидите несколько папок, каждая из которых содержит пример функций UWP.

    ![Примеры папок](images/SamplesUnzip2.png)

3.  Выберите пример, например "Высотометр", и вы увидите несколько папок с обозначением поддерживаемых языков.

    ![Языковые папки](images/SamplesUnzip3.png)

4.  Выберите язык, который требуется использовать, например CS или C\#, и вы увидите файл решения Visual Studio, который можно открыть в Visual Studio.

    ![Решение VS](images/SamplesUnzip4.png)

## <a name="give-feedback-ask-questions-and-report-issues"></a>Обратная связь, вопросы и сообщения о проблемах

Если у вас возникают проблемы или вопросы, перейдите на вкладку "Проблемы" репозитория, чтобы создать новую проблему, и мы сделаем все возможное, чтобы помочь вам решить ее.

![Изображение раздела обратной связи](images/GitHubUWPSamplesFeedback.png)

---
title: "Получение примеров универсальной платформы Windows (UWP) из GitHub"
description: "Узнайте, как скачать примеры функций UWP с GitHub"
author: JoshuaPartlow
translationtype: Human Translation
ms.sourcegitcommit: 27f98124d8360c3d0266645660b35cf040af0ef0
ms.openlocfilehash: dbd2d5d7924cfbfd48d20f48ccfcd4bfe62db645

---

#Получение примеров универсальной платформы Windows (UWP) из GitHub
Примеры приложений UWP доступны в репозиториях на GitHub. Если вы работаете с UWP, впервые, имеет смысл начать с репозитория [Microsoft/Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples), где вы найдете примеры всех функций UWP и шаблоны использования соответствующих API.  
![Репозиторий примеров UWP в GitHub](images/GitHubUWPSamplesPage.png) Дополнительные примеры доступны в разделе [Примеры](https://developer.microsoft.com/windows/samples) Центра разработки.  

##Скачать код
Чтобы скачать примеры,перейдите в [репозиторий](https://github.com/Microsoft/Windows-universal-samples) и выберите **Клонировать или скачать** и **Скачать ZIP-файл**. Либо просто нажмите [здесь](https://github.com/Microsoft/Windows-universal-samples/archive/master.zip).

В этом ZIP-файле вы всегда найдете актуальные примеры. Учетная запись GitHub для скачивания не требуется. Если выпущено обновление SDK или необходимо получить только последние изменения и добавления, просто скачайте актуальный ZIP-файл.

![Скачивание примера](images/SamplesDownloadButton.png)


> **Примечание**: для открытия, создания и запуска примеров UWP требуется Visual Studio 2015 и пакет Windows SDK. Если среда Visual Studio еще не установлена, бесплатный экземпляр Visual Studio 2015 Community Edition с поддержкой создания приложений UWP можно получить [здесь](http://go.microsoft.com/fwlink/p/?LinkID=280676).  
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

## Обратная связь, вопросы и сообщения о проблемах

Если у вас возникают проблемы или вопросы, перейдите на вкладку "Проблемы" репозитория, чтобы создать новую проблему, и мы сделаем все возможное, чтобы помочь вам решить ее.

![Изображение раздела обратной связи](images/GitHubUWPSamplesFeedback.png)



<!--HONumber=Nov16_HO1-->


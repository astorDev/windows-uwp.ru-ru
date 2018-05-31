---
author: normesta
Description: This guide explains how to configure your Visual Studio Solution to edit, debug, and package desktop app for the Desktop Bridge.
Search.Product: eADQiWindows 10XVcnh
title: Упаковка приложения с помощью Visual Studio (мост для классических приложений)
ms.author: normesta
ms.date: 08/30/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 807a99a7-d285-46e7-af6a-7214da908907
ms.localizationpriority: medium
ms.openlocfilehash: d7ae77c499cb8398aa5557f0d422899fbe8b252d
ms.sourcegitcommit: 91511d2d1dc8ab74b566aaeab3ef2139e7ed4945
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "1816259"
---
# <a name="package-an-app-by-using-visual-studio-desktop-bridge"></a>Упаковка приложения с помощью Visual Studio (мост для классических приложений)

Visual Studio можно использовать для создания пакета для классического приложения. Затем вы можете опубликовать этот пакет в Store Windows или установить его на один или несколько компьютеров без публикации.

В последней версии Visual Studio предоставляется новая версия пакета упаковки, исключающая все выполняемые вручную шаги, которые раньше требовались для упаковки приложения. Просто добавьте проект упаковки и ссылку на проект классического приложения, а затем нажмите клавишу F5, чтобы выполнить отладку приложения. Вносить изменения вручную не требуется. Это новое упрощенное взаимодействие представляет собой значительное улучшение по сравнению с возможностями предыдущей версии Visual Studio.

>[!IMPORTANT]
>Мост для классических приложений впервые появился в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для юбилейного обновления Windows 10 (10.0; сборка 14393) или более поздней версии в Visual Studio.

## <a name="first-consider-how-youll-distribute-your-app"></a>Выбор способа распространения приложения

Если вы планируете опубликовать свое приложение в [Microsoft Store](https://www.microsoft.com/store/apps), начните с заполнения [этой формы](https://developer.microsoft.com/windows/projects/campaigns/desktop-bridge). После этого Microsoft свяжется с вами, чтобы начать процесс публикации. В рамках этого процесса вы зарезервируете имя в Store и получите информацию, необходимую для упаковки приложения.

Кроме того, не забудьте изучить это руководство, прежде чем начинать создание пакета приложения: [Подготовка к упаковке приложения (мост для классических приложений)](desktop-to-uwp-prepare.md).

<a id="new-packaging-project"/>

## <a name="create-a-package"></a>Создание пакета

1. В Visual Studio откройте решение, содержащее проект вашего классического приложения.

2. Добавьте **Проект упаковки приложения для Windows** в свое решение.

   Вам не потребуется добавлять в него какой-либо код. Он используется только для создания пакета. Мы будем называть этот проект "проектом упаковки".

   ![Проект упаковки](images/desktop-to-uwp/packaging-project.png)

   >[!NOTE]
   >Этот проект отображается только в Visual Studio 2017 версии 15.5 и выше.

3. Задайте **целевую версию** проекта (любую), но не забудьте указать для параметра **Минимальная версия** значение **Юбилейное обновление Windows 10**.

   ![Диалоговое окно выбора версии упаковки](images/desktop-to-uwp/packaging-version.png)

4. В проекте упаковки щелкните правой кнопкой мыши папку **Приложения** и выберите **Добавить ссылку**.

   ![Добавление ссылки на проект](images/desktop-to-uwp/add-project-reference.png)

5. Выберите пакет классического приложения и нажмите кнопку **ОК** .

   ![Проект классического приложения](images/desktop-to-uwp/reference-project.png)

   Можно включить несколько классических приложений в пакет, но когда пользователи выбирают плитку с вашим приложением, запустить можно только одно из них. В узле **Приложения** щелкните правой кнопкой мыши приложение, которое пользователи должны запускать, выбирая плитку приложения, и выберите **Задать в качестве точки входа**.

   ![Задание точки входа](images/desktop-to-uwp/entry-point-set.png)

6. Соберите проект упаковки, чтобы убедиться, что ошибок нет.

7. Используйте мастер [Создание пакетов приложений](../packaging/packaging-uwp-apps.md), чтобы создать файл appxupload.

   Можно отправить этот файл непосредственно в Store.

**Видео**

<iframe src="https://www.youtube.com/embed/fJkbYPyd08w" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="next-steps"></a>Дальнейшие действия

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)

**Запуск, тестирование и отладка приложения**

См. [Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-debug.md)

**Улучшение классического приложения путем добавления API-интерфейсов UWP**

См. [Улучшение классического приложения для Windows 10](desktop-to-uwp-enhance.md)

**Расширьте свое классическое приложение, добавив проекты UWP и компоненты среды выполнения Windows**

См. [Расширение классического приложения с помощью современных компонентов UWP](desktop-to-uwp-extend.md)

**Распространение вашего приложения**

См. [Распространение упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-distribute.md)

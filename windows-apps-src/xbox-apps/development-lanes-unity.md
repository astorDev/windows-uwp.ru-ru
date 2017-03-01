---
author: JordanEllis6809
title: "Перенос игр на Unity в UWP на Xbox"
description: "Разработка игр на Unity для Xbox на UWP."
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: fca3267a-0c0f-4872-8017-90384fb34215
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 6c01cc5afa3b3c1266464c9f04c79cea533c9741
ms.lasthandoff: 02/08/2017

---

# <a name="bringing-unity-games-to-uwp-on-xbox"></a>Перенос игр на Unity в UWP на Xbox


В этом пошаговом руководстве предполагается, что у вас уже есть игра на движке Unity, готовая к сборке и развертыванию.

См. также [видеоверсию этого руководства](https://www.youtube.com/watch?v=f0Ptvw7k-CE).

Необходимо управлять версиями проекта UWP для Unity? См. раздел [Управление версиями проекта UWP](development-lanes-unity-versioning.md).

## <a name="step-0-ensure-unity-is-installed-correctly"></a>Шаг 0. Убедитесь, что движок Unity установлен правильно

При установке Unity необходимо выбрать следующие компоненты.

![Компоненты, устанавливаемые вместе с Unity](images/unity-install-components.png)

## <a name="step-1-building-the-uwp-solution"></a>Шаг 1. Сборка решения UWP

В проекте игры на Unity откройте окно **Параметры сборки**, последовательно выбрав пункты **Файл -> Параметры сборки**, и перейдите в меню параметров Магазина Windows, как показано ниже.

![Окно "Параметры сборки"](images/build-settings.png)

Убедитесь, что параметру **SDK** задано значение **Universal 10**, и нажмите кнопку **Сборка**, после чего запустится проводник с запросом целевой папки. Создайте папку с именем **UWP** рядом с каталогом **Ресурсы** вашего проекта и выберите эту папку в качестве целевой папки для сборки.

![Целевая папка сборки](images/build-destination.png)

Unity создаст новое решение для Visual Studio, которое мы используем для развертывания игры UWP.

![Решение VS для UWP](images/uwp-vs-solution.png)

## <a name="step-2-deploying-your-game"></a>Шаг 2. Развертывание игры

Откройте новое решение в папке **UWP** и измените целевую платформу на **x64**.

![Платформа сборки x64](images/x64-build-platform.png)

Теперь, когда у вас есть решение UWP Visual Studio для вашей игры, [выполните эти действия](getting-started.md), чтобы развернуть игру на своей коммерческой консоли Xbox One.

## <a name="step-3-modify-and-rebuild"></a>Шаг 3. Изменение и повторная сборка

Если вы вносите изменения, которые затрагивают любые компоненты, кроме сценария, то для того, чтобы эти изменения отобразились в сборке UWP для игры, в редакторе необходимо выполнить повторную сборку проекта (как описано на __шаге 1__).

## <a name="versioning-your-uwp-project"></a>Управление версиями проекта UWP

Существует ряд типичных ситуаций, когда в систему управления версиями необходимо добавить элементы этих созданных каталогов UWP. Сюда относится добавление новой зависимости в проект UWP (например, Xbox Live SDK).  Мы подробно рассматриваем этот пример в разделе [Управление версиями проекта UWP](development-lanes-unity-versioning.md).

## <a name="see-also"></a>См. также
- [Перенос существующих игр на Xbox](development-lanes-landing.md)
- [Приложения UWP для Xbox One](index.md)


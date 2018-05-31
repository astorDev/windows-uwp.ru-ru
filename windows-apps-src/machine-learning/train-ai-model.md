---
author: serenaz
title: Способы обучения модели для Windows ML в Visual Studio
description: Узнайте, как обучить модель для Windows ML с помощью Visual Studio Tools for AI в этом пошаговом руководстве.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, машинное обучение в Windows, visual studio
ms.localizationpriority: medium
ms.openlocfilehash: 5d8b50b6b8779b98de1d93f449aa560b5dcda893
ms.sourcegitcommit: 6618517dc0a4e4100af06e6d27fac133d317e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "1690220"
---
# <a name="how-to-train-a-model-for-windows-ml-in-visual-studio"></a>Способы обучения модели для Windows ML в Visual Studio
В этом руководстве мы будем использовать [Visual Studio Tools for AI](http://aka.ms/vstoolsforai), расширение разработки для создания, тестирования и развертывания решений глубинного обучения и ИИ в целях обучения модели для примера приложения MNIST раздела [Начало работы](get-started.md).

Мы будем обучать модель с помощью платформы [Microsoft Cognitive Toolkit (CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit) и [набора данных MNIST](http://yann.lecun.com/exdb/mnist/), содержащего учебный набор с 60 000 примеров и тестовый набор с 10 000 примеров рукописных цифр. Затем мы сохраним модель в формате [Open Neural Network Exchange (ONNX)](https://onnx.ai/) для использования с Windows ML. 

## <a name="prerequisites"></a>Что вам понадобится
### <a name="install-visual-studio-tools-for-ai"></a>Установите Visual Studio Tools for AI
Для начала вам потребуется скачать и установить [Visual Studio](https://www.visualstudio.com/downloads/). Открыв Visual Studio, активируйте расширение **Visual Studio Tools for AI**:

1. В меню Visual Studio выберите пункт "Расширения и обновления...".
2. Нажмите вкладку "В сети" и выберите "Поиск в Visual Studio Marketplace".
3. Найдите "Visual Studio Tools for AI". 
3. Нажмите кнопку **Скачать**. 
4. После установки перезапустите Visual Studio. 

Это расширение будет активно после перезагрузки Visual Studio. Если возникают проблемы, ознакомьтесь с разделом [Поиск расширений Visual Studio](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

### <a name="download-sample-code"></a>Загрузка примера кода
Скачайте репозиторий [Примеры для ИИ](https://github.com/Microsoft/samples-for-ai) на GitHub. Эти примеры описывают, как начать работу с глубинным обучением на примерах из TensorFlow, CNTK, Theano и других платформ.

### <a name="install-cntk"></a>Установка CNTK
Установите [CNTK для Python для Windows](https://docs.microsoft.com/en-us/cognitive-toolkit/setup-windows-python?tabs=cntkpy24). Обратите внимание, что необходимо также установить Python, если вы еще не сделали этого.

Другой способ подготовки компьютера для разработки модели глубинного обучения см. в разделе [Подготовка среды разработки](https://github.com/Microsoft/samples-for-ai/blob/master/README.md), где можно найти упрощенную процедуру для установки Python, CNTK, TensorFlow, драйверы графического процессора NVIDIA (необязательно) и многое другое.

## <a name="1-open-project"></a>1. Откройте проект

Запустите Visual Studio2015 и выберите **Файл > Открыть > Проект/Решение**. В репозитории "Примеры для ИИ" выберите папку **examples\cntk\python** и откройте файл **CNTKPythonExamples.sln**.

![Откройте решение](images/open-solution.png)

## <a name="2-train-the-model"></a>2. Обучите модель

Чтобы задать проект MNIST как запускаемый автоматически, щелкните правой кнопкой мыши по проекту python и выберите пункт **Назначить запускаемым проектом**.

![Откройте решение](images/mnist-startup.png)

Затем откройте файл ConvNet_MNIST.py и **Запустите** проект, нажав клавишу F5 или зеленую кнопку "Запустить".

## <a name="3-view-the-model-and-add-it-to-your-app"></a>3. Просмотрите модель и добавьте ее в приложение

Откройте папку **output/Models** папки в репозитории "Примеры для ИИ". В ней будет один файл обученной DNN-модели для каждой стадии эксперимента обучения, и на последнем шаге будет записан файл модели **MNIST.onnx**. 

![Откройте решение](images/onnx-model-output.png)

Теперь можно использовать этот файл обученной модели **MNIST.onnx** для создания примера приложения MNIST в примере [Начало работы](get-started.md)! 

## <a name="4-learn-more"></a>4. Подробнее
Чтобы узнать, как ускорить обучение моделей глубинного обучения с помощью [Виртуальных машин с графическими процессорами Azure](https://docs.microsoft.com/en-us/visualstudio/ai/tensorflow-vm) и получить другую информацию, посетите страницу [Искусственный интеллект в корпорации Майкрософт](https://www.microsoft.com/ai) и [Технологии машинного обучения Майкрософт](https://docs.microsoft.com/en-us/azure/machine-learning/#More-Microsoft-Machine-Learning-Technologies).
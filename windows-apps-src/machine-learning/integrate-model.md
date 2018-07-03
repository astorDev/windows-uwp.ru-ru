---
author: serenaz
title: Интеграция модели в приложения с использованием Windows ML
description: Интегрируйте модель в свое приложение по методу загрузки, привязки и оценки.
ms.author: sezhen
ms.date: 03/22/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, winml, машинное обучение в Windows
ms.localizationpriority: medium
ms.openlocfilehash: 8631c07b45a8642a1de700e424d3ca4f147456fc
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1842581"
---
# <a name="integrate-a-model-into-your-app-with-windows-ml"></a>Интеграция модели в приложения с использованием Windows ML

Средство [автоматического создания кода](overview.md#automatic-interface-code-generation) Windows ML создает интерфейс, который вызывает [API-интерфейсы Windows ML](/uwp/api/windows.ai.machinelearning.preview) за вас, что позволяет легко взаимодействовать с вашей моделью. Используя созданные с помощью интерфейса классы-оболочки, вы сможете пройти процедуру нагрузки, привязки и оценки для интеграции вашей модели машинного обучения в приложение.

![загрузка, привязка, оценка](images/load-bind-evaluate.png)

В этой статье мы будем использовать модель MNIST из раздела [Начало работы](get-started.md) для демонстрации порядка загрузки, привязки и оценки модели в вашем приложении.

## <a name="add-the-model"></a>Добавьте модель

Во-первых, необходимо добавить модель ONNX в ресурсы проекта Visual Studio. Если вы разрабатываете приложение UWP, используя [Visual Studio (версия 15.7 - Preview 1)](https://www.visualstudio.com/vs/preview/),Visual Studio автоматически создаст классы-оболочки в новом файле. Для других рабочих процессов будет необходимо использовать средство [mlgen](overview.md#automatic-interface-code-generation) для создания классов-оболочек.

Ниже приведены классы-оболочки Windows ML, созданные для модели MNIST. Далее в этой статье описываются способы использования этих классов в приложении.

```csharp
public sealed class MNISTModelInput
{
    public VideoFrame Input3 { get; set; }
}

public sealed class MNISTModelOutput
{
    public IList<float> Plus214_Output_0 { get; set; }
    public MNISTModelOutput()
    {
        this.Plus214_Output_0 = new List<float>();
    }
}

public sealed class MNISTModel
{
    private LearningModelPreview learningModel;
    public static async Task<MNISTModel> CreateMNISTModel(StorageFile file)
    {
        LearningModelPreview learningModel = await LearningModelPreview.LoadModelFromStorageFileAsync(file);
        MNISTModel model = new MNISTModel();
        model.learningModel = learningModel;
        return model;
    }
    public async Task<MNISTModelOutput> EvaluateAsync(MNISTModelInput input) {
        MNISTModelOutput output = new MNISTModelOutput();
        LearningModelBindingPreview binding = new LearningModelBindingPreview(learningModel);
        binding.Bind("Input3", input.Input3);
        binding.Bind("Plus214_Output_0", output.Plus214_Output_0);
        LearningModelEvaluationResultPreview evalResult = await learningModel.EvaluateAsync(binding, string.Empty);
        return output;
    }
}
```

**Примечание**. Чтобы обеспечить сборку модели при компиляции приложения, щелкните правой кнопкой мыши по файлу `.onnx` и выберите **Свойства**. Для **Действия при сборке** выберите **Содержимое**.

## <a name="load"></a>Загрузка

После создания классов-оболочек надо будет загрузить модель в ваше приложение.

Класс MNISTModel представляет модель MNIST, и для загрузки модели мы вызываем метод CreateMNISTModel, передав ONNX-файл в качестве параметра.

```csharp
// Load the model
StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/MNIST.onnx"));
MNISTModel model = MNISTModel.CreateMNISTModel(modelFile);
```

## <a name="bind"></a>Привязка

Созданный код содержит также входные и выходные данные классов-оболочек. Класс Input представляет ожидаемые входные данные модели, а класс Output представляет ожидаемые выходные данные модели.

Чтобы инициализировать входной объект модели, вызовите конструктор класса Input, передав данные приложения, и убедитесь, что входные данные соответствует типу входных данных, ожидаемых моделью. Класс MNISTModelInput ожидает VideoFrame, поэтому мы используем вспомогательный метод для получения VideoFrame для ввода.

```csharp
//Bind the input data to the model
MNISTModelInputs ModelInput = new MNISTModelInputs();
ModelInput.Input3 = await helper.GetHandWrittenImage(inkGrid);
```

Выходные объекты инициализируются со значениями *Null* и будут обновляться результатами модели после следующего шага, оценки.

## <a name="evaluate"></a>Оценка

После инициализации входных данных вызовите метод EvaluateAsync модели, чтобы оценить модель на входных данных. EvaluateAsync привязывает входные и выходные данные к объекту модели и оценивает модель на входных данных.

```csharp
// Evaluate the model
MNISTModelOuput ModelOutput = model.EvaluateAsync(ModelInput);
```

После оценки выходные данные содержат результаты модели, которые теперь можно просматривать и анализировать. Так как модель MNIST выводит список вероятностей, мы перебираем список для поиска и отображения цифры с наибольшей вероятностью.

```csharp
 //Iterate through output to determine highest probability digit
float maxProb = 0;
int maxIndex = 0;
for (int i = 0; i < 10; i++)
{
    if (ModelOutput.Plus214_Output_0[i] > maxProb)
    {
        maxIndex = i;
        maxProb = ModelOutput.Plus214_Output_0[i];
    }
}
numberLabel.Text = maxIndex.ToString();
```

## <a name="using-the-windows-ml-apis-directly"></a>Использование API-интерфейсов Windows ML напрямую

Несмотря на то что автоматический генератор кода Windows ML предоставляет удобный интерфейс для взаимодействия с моделью, использовать классы-оболочки необязательно. Вместо этого можно вызвать API-интерфейсы Windows ML непосредственно в вашем приложении.
Если вы решите так поступить, вы будете следовать той же процедуре: загрузка модели, привязка входных и выходных данных и оценка.

Подробные сведения об использовании API-интерфейсов см. в разделе [Справочные материалы по API Windows ML](/uwp/api/windows.ai.machinelearning.preview).

---
author: serenaz
title: Начало работы с Windows ML
description: Создайте свое первое приложение Windows ML по этому пошаговому руководству.
ms.author: sezhen
ms.date: 03/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, машинное обучение в windows, winml, windows ML
ms.localizationpriority: medium
ms.openlocfilehash: e30786f775a66bcf5c8e6dce0b4aab4f1f239be6
ms.sourcegitcommit: 91511d2d1dc8ab74b566aaeab3ef2139e7ed4945
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "1816589"
---
# <a name="get-started-with-windows-ml"></a>Начало работы с Windows ML

В этом руководстве мы создадим простое приложение UWP, которое использует обученную модель для распознавания цифр, написанных пользователем. В основном это руководство посвящено тому, как загрузить и использовать Windows ML в приложении.

## <a name="prerequisites"></a>Что вам понадобится

- [Windows SDK — сборка 17110+](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK)
- [Visual Studio (версия 15.7 — ознакомительная версия 1)](https://www.visualstudio.com/vs/preview/) 

    **Примечание**: внутри установщика Visual Studio вам потребуется снять флажок с необязательного компонента Windows 10 Preview SDK (10.0.17110.0).

## <a name="1-download-the-sample"></a>1. Скачайте пример

Во-первых, вам потребуется скачать наш [пример MNIST_GetStarted](https://github.com/Microsoft/Windows-Machine-Learning) с GitHub. Мы предлагаем шаблон с реализованными событиями и элементами управления XAML, включая:

- [InkCanvas](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) для написания цифр.
- [Кнопки](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.button) для интерпретации цифр и очистки холста.
- Вспомогательные процедуры для преобразования выходных данных InkCanvas в [VideoFrame](https://docs.microsoft.com/uwp/api/windows.media.videoframe).

Завершенный пример MNIST также доступен для загрузки с GitHub.

## <a name="2-open-project-in-visual-studio-preview"></a>2. Откройте проект в Visual Studio Preview.

Запустите Visual Studio Preview и откройте пример приложения MNIST. (Если решение отображается как недоступное, вам потребуется щелкнуть правой кнопкой мыши и выбрать «Перезагрузить проект».)

В обозревателе решений проект содержит три основных файла с кодом:

- `MainPage.xaml` - Весь наши XAML-код для создания пользовательского интерфейса для InkCanvas, кнопок и меток.
- `MainPage.xaml.cs` - Здесь находится код приложения.
- `Helper.cs` - Вспомогательные процедуры для обрезки и преобразования форматов изображений.

![Обозреватель решений Visual Studio с файлами проекта](images/get-started1.png)

## <a name="3-build-and-run-project"></a>3. Выполните сборку и запуск проекта

На панели инструментов Visual Studio смените платформу решения с «ARM» на «x64» для запуска проекта на локальном компьютере.

Чтобы запустить проект, нажмите кнопку **Начать отладку** на панели инструментов или нажмите F5. Приложение должно отображать InkCanvas, где пользователи могут написать цифру, кнопку «Распознать», чтобы интерпретировать эту цифру, поле с пустой меткой, где интерпретированная цифра отобразится в виде текста, и кнопку «Удалить цифру», чтобы очистить InkCanvas.

![Снимок экрана приложения](images/get-started2.png)

**Примечание**: если загруженная сборка новее 17110, может потребоваться изменение целевой версии развертывания для проекта. В решении проекта перейдите в раздел **Свойства**. На вкладке «Приложение» установите целевую версию в соответствии с вашей ОС и SDK.

## <a name="4-download-a-model"></a>4. Скачайте модель

Теперь получим модель машинного обучения, чтобы добавить ее в приложение. В этом учебнике мы будем использовать предварительно подготовленную **модель MNIST**, обученную с помощью [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) и [экспортированную в формате ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb).

При использовании примера MNIST_GetStarted с GitHub модель MNIST уже содержится в вашей папке ресурсов, и ее необходимо добавить в приложение в качестве существующего элемента. Также можно скачать предварительно обученную модель из раздела [ONNX Model Zoo](https://github.com/onnx/models) на GitHub.

Если вам хочется обучить собственную модель, можно использовать это [руководство](train-ai-model.md), которое применялось при обучении этой модели MNIST.

## <a name="5-add-the-model"></a>5. Добавьте модель

После загрузки модели MNIST щелкните правой кнопкой мыши в папке Assets в обозревателе решений и выберите **Добавить** > **Существующий элемент**. Наведите указатель выбора файлов на то местоположение, где находится ваша модель ONNX, и нажмите кнопку "Добавить". 

В проекте должны появиться два новых файла:

- `MNIST.onnx` - ваша обученная модель;
- `MNIST.cs` - сгенерированный Windows ML код.

![Обозреватель решений с новыми файлами](images/get-started3.png)

Чтобы обеспечить сборку модели при компиляции приложения, щелкните правой кнопкой мыши по файлу `mnist.onnx` и выберите **Свойства**. Для **Действия при сборке** выберите **Содержимое**.

Теперь рассмотрим только что созданный код в файле `MNIST.cs`. У нас есть три класса:

- **MNISTModel** создает представление модели машинного обучения, привязывает определенные входные и выходные данные к модели и асинхронно оценивает модель. 
- **MNISTModelInput** инициализирует входные типы, которые модель ожидает. В этом случае на входе ожидается VideoFrame.
- **MNISTModelOutput** инициализирует типы, которые модель произведет. В этом случае выходными данными будет список под названием classLabel типа `<long>` и словарь под названием prediction типа `<long, float>`

Теперь мы используем эти классы для загрузки, привязки и оценки модели в нашем проекте.

## <a name="6-load-bind-and-evaluate-the-model"></a>6. Загрузите, привяжите и оцените модель

Для приложений Windows ML предусмотрена следующая процедура: загрузка > привязка > оценка.

- Загрузка модели машинного обучения.
- Привязка входных и выходных данных к модели.
- Оценка модели и просмотр результатов.

Мы будем использовать код интерфейса, созданный в `MNIST.cs`, для загрузки, привязки и оценки модели в нашем приложении.

Во-первых, в `MainPage.xaml.cs` создадим экземпляр модели, входные и выходные данные.

```csharp
namespace MNIST_Demo
{
    public sealed partial class MainPage : Page
    {
        private MNISTModel ModelGen = new MNISTModel();
        private MNISTModelInput ModelInput = new MNISTModelInput();
        private MNISTModelOutput ModelOutput = new MNISTModelOutput();
        ...
    }
}
```

Затем, в LoadModel(), мы загрузим модель.

```csharp
private async void LoadModel()
{
    //Load a machine learning model
    StorageFile modelFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/MNIST.onnx"));
    ModelGen = await MNISTModel.CreateMNISTModel(modelFile);
}
```

Далее необходимо привязать наших входные и выходные данные к модели. 

В этом случае наша модель ожидает на входе тип VideoFrame. С помощью наших включенных вспомогательных функций в `helper.cs` мы будем копировать содержимое InkCanvas, преобразовывать его в VideoFrame и привязывать его к нашей модели.

```csharp
private async void recognizeButton_Click(object sender, RoutedEventArgs e)
{
     //Bind model input with contents from InkCanvas
     ModelInput.Input3 = await helper.GetHandWrittenImage(inkGrid);
}
```

Для вывода мы просто вызовем метод EvaluateAsync() с использованием указанных входных параметров. Так как модель возвращает список цифр с соответствующей вероятностью, нам необходимо проанализировать возвращенный список для определения того, какой цифре соответствует наибольшая вероятность, и отобразить эту цифру.

```csharp
private async void recognizeButton_Click(object sender, RoutedEventArgs e)
{
    //Bind model input with contents from InkCanvas
    ModelInput.Input3 = await helper.GetHandWrittenImage(inkGrid);
    
    //Evaluate the model
    ModelOutput = await ModelGen.EvaluateAsync(ModelInput);
            
    //Iterate through evaluation output to determine highest probability digit
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
}
```

Наконец, нам потребуется очистить InkCanvas, чтобы пользователи смогли написать другую цифру.

```csharp
private void clearButton_Click(object sender, RoutedEventArgs e)
{
    inkCanvas.InkPresenter.StrokeContainer.Clear();
    numberLabel.Text = "";
}
```

## <a name="7-launch-the-app"></a>7. Запустите приложение

Как только мы выполним сборку и запустим приложение, мы сможем распознать цифру, написанную на InkCanvas.

![завершите приложение](images/get-started4.png)

## <a name="8-next-steps"></a>8. Дальнейшие действия

Вот и все — вы создали свое первое приложение Windows ML! Дополнительные примеры, демонстрирующие способы использования Windows ML, см. в разделе [Примеры приложений](samples.md).
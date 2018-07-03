---
author: wschin
title: Преобразование существующих моделей ML в ONNX
description: В примерах кода демонстрируется использование WinMLTools для преобразования существующих моделей в scikit-learn и Core ML в формат ONNX.
ms.author: sezhen
ms.date: 3/7/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, машинное обучение в windows, WinML, WinMLTools, ONNX, ONNXMLTools, scikit-learn, Core ML
ms.localizationpriority: medium
ms.openlocfilehash: 7b2e9c8b661ccd2b0358882992da6c4f160b49f0
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1843004"
---
# <a name="convert-existing-ml-models-to-onnx"></a>Преобразование существующих моделей ML в ONNX

[WinMLTools](https://pypi.org/project/winmltools/) позволяет пользователям преобразовать модели, обученные в других платформах, в формат ONNX. Здесь рассказывается, как установить пакет WinMLTools и преобразовать существующие модели в scikit-learn и Core ML в ONNX с помощью кода на Python.

## <a name="install-winmltools"></a>Установка WinMLTools

WinMLTools — это пакет Python (winmltools), который поддерживает Python версий 2.7 и 3.6. При работе над проектом анализа данных рекомендуется установить научный дистрибутив Python, например Anaconda.

WinMLTools использует [стандартный процесс установки пакета python](https://packaging.python.org/installing/). Из среды python выполните следующую команду:

```
pip install winmltools
```

WinMLTools имеет следующие зависимости:

- numpy v1.10.0+
- onnxmltools 0.1.0.0+
- protobuf v.3.1.0+

Чтобы обновить зависимые пакеты, запустите команду pip с аргументом "-U".

```
pip install -U winmltools
```

Подпишитесь на [onnxmltools](https://github.com/onnx/onnxmltools) на GitHub для получения дополнительных сведений о зависимостях onnxmltools.

Дополнительные сведения о том, как использовать WinMLTools, можно найти в документации по конкретному пакету с помощью функции справки.

```
help(winmltools)
```

> [!NOTE]
> С расширением Visual Studio Tools for AI можно также использовать WinMLTools в среде IDE Visual Studio для реализации более понятных, последовательных условий работы при преобразовании моделей в формат ONNX. Дополнительные сведения см. в разделе [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai/).

## <a name="scikit-learn-models"></a>Модели scikit-learn

В следующем фрагменте кода метод опорных векторов обучается в scikit-learn, и модель преобразуется в ONNX.

~~~python
# First, we create a toy data set.
# The training matrix X contains three examples, with two features each.
# The label vector, y, stores the labels of all examples.
X = [[0.5, 1.], [-1., -1.5], [0., -2.]]
y = [1, -1, -1]

# Then, we create a linear classifier and train it.
from sklearn.svm import LinearSVC
linear_svc = LinearSVC()
linear_svc.fit(X, y)

# To convert scikit-learn models, we need to specify the input feature's name and type for our converter. 
# The following line means we have a 2-D float feature vector, and its name is "input" in ONNX.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from winmltools import convert_sklearn
linear_svc_onnx = convert_sklearn(linear_svc, name='LinearSVC', input_features=[('input', 'float', 2)])    

# Now, we save the ONNX model into binary format.
from winmltools.utils import save_model
save_model(linear_svc_onnx, 'linear_svc.onnx')

# If you'd like to load an ONNX binary file, our tool can also help.
from winmltools.utils import load_model
linear_svc_onnx = load_model('linear_svc.onnx')

# To see the produced ONNX model, we can print its contents or save it in text format.
print(linear_svc_onnx)
from winmltools.utils import save_text
save_text(linear_svc_onnx, 'linear_svc.txt')

# The conversion of linear regression is very similar. See the example below.
from sklearn.svm import LinearSVR
linear_svr = LinearSVR()
linear_svr.fit(X, y)
linear_svr_onnx = convert_sklearn(linear_svr, name='LinearSVR', input_features=[('input', 'float', 2)])   
~~~

Пользователи могут заменить `LinearSVC` другими моделями scikit-learn, такими как `RandomForestClassifier`. Обратите внимание, что [автоматический генератор кода](overview.md#automatic-interface-code-generation) использует параметр `name` для создания имен классов и переменных. Если `name` не предоставляется, создается идентификатор GUID, который не будет соответствовать соглашениям об именовании для таких языков, как C++/C#. 

## <a name="scikit-learn-pipelines"></a>Конвейеры scikit-learn

Далее рассмотрим, как конвейеры scikit-learn можно преобразовать в ONNX.

~~~python
# First, we create a toy data set.
# Notice that the first example's last feature value, 300, is large.
X = [[0.5, 1., 300.], [-1., -1.5, -4.], [0., -2., -1.]]
y = [1, -1, -1]

# Then, we declare a linear classifier.
from sklearn.svm import LinearSVC
linear_svc = LinearSVC()

# One common trick to improve a linear model's performance is to normalize the input data.
from sklearn.preprocessing import Normalizer
normalizer = Normalizer()

# Here, we compose our scikit-learn pipeline. 
# First, we apply our normalization. 
# Then we feed the normalized data into the linear model.
from sklearn.pipeline import make_pipeline
pipeline = make_pipeline(normalizer, linear_svc)
pipeline.fit(X, y)

# Now, we convert the scikit-learn pipeline into ONNX format. 
# Compared to the previous example, notice that the specified feature dimension becomes 3.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from winmltools import convert_sklearn
pipeline_onnx = convert_sklearn(linear_svc, name='NormalizerLinearSVC', input_features=[('input', 'float', 3)])   

# We can print the fresh ONNX model.
print(pipeline_onnx)

# We can also save the ONNX model into binary file for later use.
from winmltools.utils import save_model
save_model(pipeline_onnx, 'pipeline.onnx')

# Our conversion framework provides limited support of heterogeneous feature values.
# We cannot have numerical types and string type in one feature vector. 
# Let's assume that the first two features are floats and the last feature is integer (encoded a categorical attribute).
X_heter = [[0.5, 1., 300], [-1., -1.5, 400], [0., -2., 100]]

# One popular way to represent categorical is one-hot encoding.
from sklearn.preprocessing import OneHotEncoder
one_hot_encoder = OneHotEncoder(categorical_features=[2])

# Let's initialize a classifier. 
# It will be right after the one-hot encoder in our pipeline.
linear_svc = LinearSVC()

# Then, we form a two-stage pipeline.
another_pipeline = make_pipeline(one_hot_encoder, linear_svc)
another_pipeline.fit(X_heter, y)

# Now, we convert, save, and load the converted model. 
# For heterogeneous feature vectors, we need to seperately specify their types for all homogeneous segments.
# The automatic code generator (mlgen) uses the name parameter to generate class names.
another_pipeline_onnx = convert_sklearn(another_pipeline, name='OneHotLinearSVC', input_features=[('input', 'float', 2), ('another_input', 'int64', 1)])
save_model(another_pipeline_onnx, 'another_pipeline.onnx')
from winmltools.utils import load_model
loaded_onnx_model = load_model('another_pipeline.onnx')

# Of course, we can print the ONNX model to see if anything went wrong.
print(another_pipeline_onnx)
~~~

## <a name="core-ml-models"></a>Модели Core ML

Здесь мы предполагаем, что путь к файлу примера модели Core ML такой: *example.mlmodel*.

~~~python
from coremltools.models.utils import load_spec
# Load model file
model_coreml = load_spec('example.mlmodel')
from winmltools import convert_coreml
# Convert it!
# The automatic code generator (mlgen) uses the name parameter to generate class names.
model_onnx = convert_coreml(model_coreml, name='ExampleModel')   
~~~

`model_onnx` является объектом [ModelProto](https://github.com/onnx/onnxmltools/blob/0f453c3f375c1ae928b83a4c7909c82c013a5bff/onnxmltools/proto/onnx-ml.proto#L176) в ONNX. Его можно сохранить в двух разных форматах.

~~~python
from winmltools.utils import save_model
# Save the produced ONNX model in binary format
save_model(model_onnx, 'example.onnx')
# Save the produced ONNX model in text format
from winmltools.utils import save_text
save_text(model_onnx, 'example.txt')
~~~

**Примечание**: CoreMLTools — это пакет Python, предоставляемый Apple, но он недоступен в Windows. Если необходимо установить этот пакет в Windows, установите пакет непосредственно из репозитория:

```
pip install git+https://github.com/apple/coremltools
```

## <a name="core-ml-models-with-image-inputs-or-outputs"></a>Модели Core ML с входными и выходными данными в виде изображений

Из-за отсутствия типов изображений в ONNX преобразование моделей изображений Core ML (т. е. моделей, в которых изображения используются в качестве входных или выходных данных) требует некоторых действий для предварительной обработки и постобработки.

Целью предварительной обработки является обеспечение того, чтобы входное изображение было правильно отформатировано в виде тензора ONNX. Предположим, *X* — это входное изображение с формой [C, H, W] в Core ML. В ONNX переменная *X* будет тензором с плавающей запятой с той же формой, а *X*[0, :, :]/*X*[1, :, :]/*X*[2, :, :] сохраняет красный, зеленый и синий каналы изображения. Для изображений в Core ML в оттенках серого в ONNX форматом являются [1, H, W]-тензоры, поскольку используется только один канал.

Если первоначальная модель Core ML выводит изображение, вручную преобразуйте выходные тензоры с плавающей запятой ONNX обратно в изображения. Предусмотрены два простых этапа. Первым шагом является усечение значений больше 255 до 255 и замена всех отрицательных значений на 0. Второй шаг — округлить все значения пикселей до целых чисел (путем добавления 0,5 и усечения дробной части). Порядок выходных каналов (например, RGB или BGR) указан в модели Core ML. Для создания верного выходного изображения нам может потребоваться выполнить транспозицию или отображение в случайном порядке для восстановления требуемого формата.

Здесь мы рассмотрим модель Core ML, FNS-Candy, скачанную с [GitHub](https://github.com/likedan/Awesome-CoreML-Models), в качестве конкретного примера преобразования для демонстрации разницы между форматами ONNX и Core ML. Обратите внимание, что все последующие команды выполняются в среде python.

Во-первых, мы загружаем модель Core ML и проверяем ее входной и выходной форматы.

~~~python
from coremltools.models.utils import load_spec
model_coreml = load_spec('FNS-Candy.mlmodel')
model_coreml.description # Print the content of Core ML model description
~~~

Вывод на экран:

~~~
...
input {
    ...
      imageType {
      width: 720
      height: 720
      colorSpace: BGR
    ...
}
...
output {
    ...
      imageType {
      width: 720
      height: 720
      colorSpace: BGR
    ...
}
...
~~~

В нашем случае входным и выходным форматом является изображение 720 x 720 BGR. Следующий шаг — преобразование модели Core ML с помощью WinMLTools.

~~~python
# The automatic code generator (mlgen) uses the name parameter to generate class names.
from onnxmltools import convert_coreml
model_onnx = convert_coreml(model_coreml, name='FNSCandy')    
~~~

Альтернативный метод для просмотра форматов ввода и вывода модели в ONNX — использовать следующую команду:

~~~python
model_onnx.graph.input # Print out the ONNX input tensor's information
~~~

Вывод на экран:

~~~
...
  tensor_type {
    elem_type: FLOAT
    shape {
      dim {
        dim_param: "None"
      }
      dim {
        dim_value: 3
      }
      dim {
        dim_value: 720
      }
      dim {
        dim_value: 720
      }
    }
  }
...
~~~

Полученные входные данные (обозначенные *X*) в ONNX представляют собой четырехмерный тензор. Последними тремя осями являются: C, H и W, соответственно. Первое измерение — «None», и это означает, что эта модель ONNX может применяться к любому количеству изображений. Для применения этой модели в целях обработки пакета из 2 изображений первое изображение соответствует *X*[0, :, :, :], тогда как *X*[1, :, :, :] соответствует второму изображению. Синий, зеленый и красный каналы первого изображения — *X*[0, 0, :, :]/*X*[0, 1, :, :]/*X*[0, 2, :, :]; аналогично для второго изображения.

~~~python
model_onnx.graph.output # Print out the ONNX output tensor's information
~~~

Вывод на экран:

~~~
...
  tensor_type {
    elem_type: FLOAT
    shape {
      dim {
        dim_param: "None"
      }
      dim {
        dim_value: 3
      }
      dim {
        dim_value: 720
      }
      dim {
        dim_value: 720
      }
    }
  }
...
~~~

Как можно увидеть, создаваемый формат идентичен формату входных данных исходной модели. Тем не менее, в этом случае это не изображение, поскольку значения пикселей представлены целыми числами, а не числами с плавающей запятой. Чтобы преобразовать обратно в изображение, усеките числа больше 255 до 255, измените отрицательные значения на 0 и округлите все десятичные до целых чисел.
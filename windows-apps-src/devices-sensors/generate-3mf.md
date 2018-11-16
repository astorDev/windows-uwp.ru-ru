---
author: PatrickFarley
Description: Describes the structure of the 3D Manufacturing Format file type and how it can be created and manipulated with the Windows.Graphics.Printing3D API.
MS-HAID: dev\_devices\_sensors.generate\_3mf
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Создание пакета 3MF
ms.assetid: 968d918e-ec02-42b5-b50f-7c175cc7921b
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9d7acfdb6312770f51d8870549218344ad8c4330
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6846730"
---
# <a name="generate-a-3mf-package"></a>Создание пакета 3MF

**Важные API**

-   [**Windows.Graphics.Printing3D**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.aspx)

В этом руководстве приводится описание структуры документа формата создания 3D и способов его создания и обработки с помощью API [**Windows.Graphics.Printing3D**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.aspx).

## <a name="what-is-3mf"></a>Что такое 3MF?

Формат создания 3D — это набор соглашений об использовании XML для описания вида и структуры трехмерных моделей для производства (трехмерной печати). Он определяет набор компонентов (обязательных и необязательных) и их связи для предоставления всех необходимых сведений устройству для трехмерной печати. Набор данных, соответствующий формату создания 3D, можно сохранять как файл с расширением .3mf.

В Windows10 [**Printing3D3MFPackage**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.printing3d3mfpackage.aspx) класса в пространстве имен **Windows.Graphics.Printing3D** аналогичен одному 3mf-файлу и другие классы соответствуют определенным элементам XML в файл. В этом руководстве описывается, как создать и настроить основные части документа 3MF программными средствами, как использовать расширение 3MF Materials и как преобразовать объект **Printing3D3MFPackage** и сохранить его в виде 3MF-файла. Подробнее о стандартах 3MF и расширении 3MF Materials см. в разделе [3MF Specification](http://3mf.io/what-is-3mf/3mf-specification/).

<!-- >**Note** This guide describes how to construct a 3MF document from scratch. If you wish to make changes to an already existing 3MF document provided in the form of a .3mf file, you simply need to convert it to a **Printing3D3MFPackage** and alter the contained classes/properties in the same way (see [link]) below). -->


## <a name="core-classes-in-the-3mf-structure"></a>Основные классы в структуре 3MF

Класс **Printing3D3MFPackage** представляет полный документ 3MF. В основе документа 3MF лежит часть модели, представленная классом [**Printing3DModel**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.printing3dmodel.aspx). Большая часть сведений, которые мы хотим указать о трехмерной модели, хранятся в свойствах класса **Printing3DModel** и свойствах базовых классов.

[!code-cs[InitClasses](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetInitClasses)]

<!-- >**Note** We do not yet associate the **Printing3D3MFPackage** with its corresponding **Printing3DModel** object. Only after fleshing out the **Printing3DModel** with all of the information we wish to specify will we make that association (see [link]). -->

## <a name="metadata"></a>Метаданные

Часть модели документа 3MF может содержать метаданные в виде пар "ключ/значение", которые хранятся в свойстве **Metadata**. Существует ряд предопределенных имен метаданных, но можно добавить и другие пары как часть расширения (это описано более подробно в [спецификации 3MF](http://3mf.io/what-is-3mf/3mf-specification/). Получатель пакета (устройство трехмерной печати) определяет, следует ли обрабатывать метаданные и как это делать, но рекомендуется добавлять в пакет 3MF как можно больше базовых сведений:

[!code-cs[Metadata](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetMetadata)]

## <a name="mesh-data"></a>Данные сетки

В контексте этого руководства сетка — это тело трехмерной геометрии, созданное из одного набора вершин (хотя оно не должна отображаться как сплошное тело). Часть сетки представлена классом [**Printing3DMesh**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.printing3dmesh.aspx). Допустимый объект сетки должен содержать информацию о местоположении всех вершин, а также всех треугольных граней между некоторыми наборами вершин.

Следующий метод добавляет вершины в сетку и указывает их расположение в трехмерном пространстве.

[!code-cs[Vertices](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetVertices)]

Следующий метод определяет все треугольники, которые необходимо нарисовать для этих вершин:

[!code-cs[TriangleIndices](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetTriangleIndices)]

> [!NOTE]
> Индексы всех треугольников должны быть определены против часовой стрелки (при просмотре треугольника за пределами объекта сетки), чтобы их векторы-нормали к граням были направлены наружу.

Когда объект Printing3DMesh будет содержать допустимые наборы вершин и треугольников, его можно добавить в свойство **Meshes** модели. Все объекты **Printing3DMesh** в пакете должны храниться в свойстве **Meshes** класса **Printing3DModel**.

[!code-cs[MeshAdd](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetMeshAdd)]


## <a name="create-materials"></a>Создание материалов


Трехмерная модель может содержать данные для нескольких материалов. Это соглашение позволяет воспользоваться устройствами трехмерной печати, которые могут использовать несколько материалов в одном задании печати. Также существует несколько *типов* групп материалов, каждый из которых поддерживает нескольких различных материалов. У каждой группы материалов должен быть уникальный ссылочный номер, а у всех материалов в этой группе также должен быть уникальный идентификатор

Различные объекты сетки в модели могут ссылаться на эти материалы. Кроме того, отдельные треугольники для каждой сетки могут определять различные материалы. Кроме того, различные материалы могут быт даже представлены одним треугольником, каждой вершине которого назначен разный материал, при этом материал грани вычисляется как градиент между ними.

В этом руководстве показано, как создавать различные типы материалов в соответствующих группах и сохранять их как ресурсы в объекте модели. Затем мы рассмотрим назначение различных материалов отдельным сеткам и треугольникам.

### <a name="base-materials"></a>Базовые материалы

Тип материалов по умолчанию — это **базовый материал**, у которого есть значение **Цветной материал** (описано ниже) и атрибут имени, указывающий *тип* используемого материала.

[!code-cs[BaseMaterialGroup](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetBaseMaterialGroup)]

> [!NOTE]
> Устройство трехмерной печати определит, какие доступные физические материалы соответствуют виртуальным материалам в 3MF. Сопоставление материалов не должно быть однозначным: если в трехмерном принтере используется только один материал, он напечатает всю модель из этого материала независимо от того, какие объекты или грани были назначены различным материалам.

### <a name="color-materials"></a>Цветные материалы

**Цветные материалы** аналогичны **базовым**, но у них нет имени. Таким образом они не предоставляют никаких инструкции о типе материала, который будет использовать устройство. Они содержат только данные о цветах и позволяют принтеру выбрать тип материала (устройство также может предложить пользователю выбрать). В коде ниже объекты `colrMat` из предыдущего метода используются самостоятельно.

[!code-cs[ColorMaterialGroup](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetColorMaterialGroup)]

### <a name="composite-materials"></a>Композитные материалы

**Композитные материалы** указывают устройству, что необходимо использовать согласованную смесь разных **базовых материалов**. Каждая **группа композитных материалов** должна ссылать строго на одну **группу базовых материалов**, из которой берутся ингредиенты. Кроме того, **базовые материалы** в этой группе, которые необходимо сделать доступными, должны быть указаны в списке **индексов материалов**, на которые будет ссылаться каждый **композитный материал** при указании коэффициентов (каждый **композитный материал** — это просто отношение **базовых материалов**).

[!code-cs[CompositeMaterialGroup](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetCompositeMaterialGroup)]

### <a name="texture-coordinate-materials"></a>Материалы координат текстуры

3MF поддерживает использование двумерных изображений для отображения цвета на поверхности трехмерных моделей. Таким образом модель может передавать гораздо больше данных цветов для каждой грани треугольника (вместо одного значения цвета для каждой вершины из треугольников). Как и **цветные материалы**, материалы координат текстур только передают данные цветов. Чтобы использовать двумерную текстуру, сначала необходимо объявить ресурс текстуры.

[!code-cs[TextureResource](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetTextureResource)]

> [!NOTE]
> Данные текстуры относятся к самому пакету 3MF, а не к части модели в пакете.

Далее нам необходимо заполнить **материалы Texture3Coord**. Каждый из них ссылается на ресурс текстуры и указывает определенную точку на изображении (в координатах UV).

[!code-cs[Texture2CoordMaterialGroup](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetTexture2CoordMaterialGroup)]

## <a name="map-materials-to-faces"></a>Сопоставление материалов с гранями

Чтобы указать, какие материалы сопоставляются с теми или иными вершинами каждого треугольника, необходимо поработать с объектом сетки нашей модели (если модель содержит несколько сеток, им следует отдельно назначить материалы). Как упоминалось выше, материалам назначаются каждой вершине каждого треугольника. Сведения о том, как эти сведения вводятся и интерпретируются, см. в коде ниже.

[!code-cs[MaterialIndices](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetMaterialIndices)]

## <a name="components-and-build"></a>Компоненты и сборка

Структура компонента позволяет пользователю разместить несколько объектов сетки в печатаемой трехмерной модели. Объект [**Printing3DComponent**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.printing3dcomponent.aspx) содержит одну сетку и список ссылок на другие компоненты. Фактически это список объектов [**Printing3DComponentWithMatrix**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing3d.printing3dcomponentwithmatrix.aspx). Каждый объект **Printing3DComponentWithMatrix** содержит **Printing3DComponent** и, что важно, матрицу преобразования, которая применяется к сетке и компонентам **Printing3DComponent**.

Например, модель автомобиля может состоять из кузова **Printing3DComponent**, который содержит сетку кузова автомобиля. Компонент Body может содержать ссылки на четыре разных объекта **Printing3DComponentWithMatrix**, которые ссылаются на один объект **Printing3DComponent** с сеткой Wheel и содержат четыре разных матрицы преобразования (сопоставляющие колеса с различными позициями на кузове автомобиля). В этом сценарии сетку Body и сетку Wheel необходимо сохранить только один раз, хотя конечный продукт в итоге будет содержать пять сеток.

Все объекты **Printing3DComponent** необходимо напрямую указать в свойстве **Components** модели. Компонент, который будет использоваться в задании печати, хранится в свойстве **Build**.

[!code-cs[Components](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetComponents)]

## <a name="save-package"></a>Сохранение пакета
Получив модель с заданными материалами и компонентами, мы можем сохранить ее в пакете.

[!code-cs[SavePackage](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetSavePackage)]

Затем мы можем запустить задание печати в приложении (см. раздел [Трехмерная печать из приложения](https://msdn.microsoft.com/library/windows/apps/mt204541.aspx)) или сохранить пакет **Printing3D3MFPackage** как 3MF-файл.

Следующий метод принимает готовый пакет **Printing3D3MFPackage** и сохраняет данные в 3MF-файле.

[!code-cs[SaveTo3mf](./code/3dprinthowto/cs/Generate3MFMethods.cs#SnippetSaveTo3mf)]

## <a name="related-topics"></a>Еще по теме

[Трехмерная печать из приложения](https://msdn.microsoft.com/windows/uwp/devices-sensors/3d-print-from-app)  
[Пример трехмерной печати UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/3DPrinting)
 

 

 

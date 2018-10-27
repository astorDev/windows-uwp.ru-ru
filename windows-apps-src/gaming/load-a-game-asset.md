---
author: mtoepke
title: Загрузка ресурсов в игре DirectX
description: В большинстве игр в определенный момент выполняется загрузка ресурсов (например, построителей текстуры, текстур, ранее созданных mesh-объектов или других графических данных) из локального хранилища данных или другого потока данных.
ms.assetid: e45186fa-57a3-dc70-2b59-408bff0c0b41
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, загрузка ресурсов
ms.localizationpriority: medium
ms.openlocfilehash: 1bea3f515ba8ff810fc6dfd6281f0488c4f3e235
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5691398"
---
# <a name="load-resources-in-your-directx-game"></a>Загрузка ресурсов в игре DirectX



В большинстве игр в определенный момент выполняется загрузка ресурсов (например, шейдеров, текстур, ранее созданных mesh-объектов или других графических данных) из локального хранилища данных или другого потока данных. В этом руководстве мы в общих чертах рассмотрим, какие вопросы следует учитывать при загрузке этих файлов для использования в игре универсальной платформы Windows (UWP) на C/C++ с DirectX.

Например, mesh-объекты для полигональных объектов вашей игры можно создать с помощью другого программного средства и экспортировать в определенный формат. То же справедливо и в отношении текстур более того: несмотря на то, что плоские несжатые точечные рисунки могут быть созданы при помощи большинства программных средств и с ними может работать большая часть графических API, их использование в игре является крайне неэффективным. В этом руководстве мы подробно рассмотрим основные этапы загрузки трех разных типов графических ресурсов— mesh-объектов (моделей), текстур (точечных рисунков) и скомпилированных шейдеров— для использования с Direct3D.

## <a name="what-you-need-to-know"></a>Что необходимо знать


### <a name="technologies"></a>Технологии

-   Библиотека параллельных шаблонов (ppltasks.h)

### <a name="prerequisites"></a>Необходимые условия

-   Среда выполнения Windows
-   Асинхронные задачи
-   Основные концепции программирования трехмерной графики.

Этот образец также включает три файла с кодом для загрузки и управления ресурсами. В этом разделе вы часто будете встречать программные объекты, определенные в следующих файлах.

-   BasicLoader.h/.cpp 
-   BasicReaderWriter.h/.cpp
-   DDSTextureLoader.h/.cpp

Полный код для этих примеров можно найти по следующим ссылкам.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="complete-code-for-basicloader.md">Полный код для BasicLoader</a></p></td>
<td align="left"><p>Полный код класса и методов для преобразования и загрузки в память графических mesh-объектов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="complete-code-for-basicreaderwriter.md">Полный код класса BasicReaderWriter</a></p></td>
<td align="left"><p>Полный код класса и методов для чтения и записи двоичных файлов данных без ограничений. Используется классом <a href="complete-code-for-basicloader.md">BasicLoader</a>.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="complete-code-for-ddstextureloader.md">Полный код класса DDSTextureLoader</a></p></td>
<td align="left"><p>Полный код класса и метода для загрузки из памяти текстуры DDS.</p></td>
</tr>
</tbody>
</table>

 

## <a name="instructions"></a>Инструкции

### <a name="asynchronous-loading"></a>Асинхронная загрузка

Асинхронная загрузка выполняется при помощи шаблона **task** из библиотеки параллельных шаблонов (PPL). Шаблон **task** содержит метод, который может дополняться лямбда-функцией для обработки результатов асинхронного вызова после его завершения и обычно имеет формат

`task<generic return type>(async code to execute).then((parameters for lambda){ lambda code contents });`.

Используя синтаксис **.then()**, можно создать цепочку задач, чтобы при завершении одной операции могла выполняться другая асинхронная операция, которая зависит от результатов предыдущей операции. Таким образом можно загружать комплексные ресурсы, преобразовывать их и управлять ими в отдельных потоках почти незаметно для пользователя игры.

Дополнительные сведения см. в разделе [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

Теперь давайте рассмотрим базовую архитектуру объявления и создания асинхронного метода загрузки файлов **ReadDataAsync**.

```cpp
#include <ppltasks.h>

// ...
concurrency::task<Platform::Array<byte>^> ReadDataAsync(
        _In_ Platform::String^ filename);

// ...

using concurrency;

task<Platform::Array<byte>^> BasicReaderWriter::ReadDataAsync(
    _In_ Platform::String^ filename
    )
{
    return task<StorageFile^>(m_location->GetFileAsync(filename)).then([=](StorageFile^ file)
    {
        return FileIO::ReadBufferAsync(file);
    }).then([=](IBuffer^ buffer)
    {
        auto fileData = ref new Platform::Array<byte>(buffer->Length);
        DataReader::FromBuffer(buffer)->ReadBytes(fileData);
        return fileData;
    });
}
```

В этом примере кода при вызове определенного выше метода **ReadDataAsync** создается задача чтения буфера из файловой системы. После завершения этой задачи цепочка задач принимает содержимое буфера и передает поток байтов из буфера в массив с помощью статического типа [**DataReader**](https://msdn.microsoft.com/library/windows/apps/br208119).

```cpp
m_basicReaderWriter = ref new BasicReaderWriter();

// ...
return m_basicReaderWriter->ReadDataAsync(filename).then([=](const Platform::Array<byte>^ bytecode)
    {
      // Perform some operation with the data when the async load completes.          
    });
```

Код вызова метода **ReadDataAsync**. Когда он завершает работу, ваша программа получает массив байтов, считанный из предоставленного файла. Поскольку сам метод **ReadDataAsync** определен в формате задачи, вы можете использовать лямбда-функцию, чтобы выполнить определенную операцию при возвращении массива байтов, например передать байтовые данные в функцию DirectX, которая может их использовать.

Если ваша игра не слишком сложная, загружайте ресурсы с помощью подобного метода при запуске игры. Это можно сделать до запуска основного цикла игры в какой-либо момент последовательности вызовов вашей реализации [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505). Точно так же, вызов методов загрузки ресурсов может выполняться асинхронно, чтобы игра быстрее запускалась и пользователю игры не приходилось ждать окончания загрузки до выполнения каких-либо действий на ранних этапах.

И все же мы не хотим, чтобы можно было использовать все функции игры до завершения асинхронной загрузки всех ресурсов. Создайте метод, который будет сигнализировать о завершении загрузки, например устанавливать флажок для специального поля, и используйте лямбда-функции в методе загрузки для настройки этого сигнала при завершении загрузки. Перед запуском любого компонента, использующего загружаемые ресурсы, необходимо выполнить проверку переменной.

Ниже приводится пример, в котором асинхронные методы, определенные в файле BasicLoader.cpp, используются для загрузки шейдеров, mesh-объектов и текстур при запуске игры. Обратите внимание, что в данном примере после завершения работы всех методов-загрузчиков в специальном поле свойств объекта игры, **m\_loadingComplete**, устанавливается флажок.

```cpp
void ResourceLoading::CreateDeviceResources()
{
    // DirectXBase is a common sample class that implements a basic view provider. 
    
    DirectXBase::CreateDeviceResources(); 

    // ...

    // This flag will keep track of whether or not all application
    // resources have been loaded.  Until all resources are loaded,
    // only the sample overlay will be drawn on the screen.
    m_loadingComplete = false;

    // Create a BasicLoader, and use it to asynchronously load all
    // application resources.  When an output value becomes non-null,
    // this indicates that the asynchronous operation has completed.
    BasicLoader^ loader = ref new BasicLoader(m_d3dDevice.Get());

    auto loadVertexShaderTask = loader->LoadShaderAsync(
        "SimpleVertexShader.cso",
        nullptr,
        0,
        &m_vertexShader,
        &m_inputLayout
        );

    auto loadPixelShaderTask = loader->LoadShaderAsync(
        "SimplePixelShader.cso",
        &m_pixelShader
        );

    auto loadTextureTask = loader->LoadTextureAsync(
        "reftexture.dds",
        nullptr,
        &m_textureSRV
        );

    auto loadMeshTask = loader->LoadMeshAsync(
        "refmesh.vbo",
        &m_vertexBuffer,
        &m_indexBuffer,
        nullptr,
        &m_indexCount
        );

    // The && operator can be used to create a single task that represents
    // a group of multiple tasks. The new task's completed handler will only
    // be called once all associated tasks have completed. In this case, the
    // new task represents a task to load various assets from the package.
    (loadVertexShaderTask && loadPixelShaderTask && loadTextureTask && loadMeshTask).then([=]()
    {
        m_loadingComplete = true;
    });

    // Create constant buffers and other graphics device-specific resources here.
}
```

Кроме того, обратите внимание, что эти задачи можно агрегировать при помощи оператора &&, чтобы лямбда-функция, устанавливающая флажок окончания загрузки, запускалась только после завершения всех задач. Заметьте, что, если вы определили несколько флажков, существует вероятность возникновения состояний гонки. Например, если лямбда-функция последовательно устанавливает два флажка для одного и того же значения, другой поток может видеть только один набор флажков, если он проверяет их до установки следующего набора флажков.

Мы рассмотрели асинхронную загрузку файлов ресурсов. Синхронная загрузка файлов намного проще; ее примеры доступны в разделах [Полный код модуля BasicReaderWriter](complete-code-for-basicreaderwriter.md) и [Полный код модуля BasicLoader](complete-code-for-basicloader.md).

Конечно, для различных ресурсов часто оказывается необходимой их дополнительная обработка или преобразование до использования в вашем графическом конвейере. Давайте рассмотрим три особых типа ресурсов: mesh-объекты, текстуры и шейдеры.

### <a name="loading-meshes"></a>Загрузка mesh-объектов

Mesh-объекты представляют собой набор данных о вершинах полигональной сетки — либо программно сгенерированные в самой игре, либо экспортированные из файлов, созданных в других приложениях (например, 3DStudio MAX или Alias WaveFront) или программных средствах. Mesh-объекты представляют в игре модели: от простых примитивов типа кубов или сфер до автомобилей, зданий и персонажей. Кроме того, они часто содержат сведения о параметрах цвета и анимации, если это допускает формат. Мы рассмотрим mesh-объекты, содержащие только сведения о вершинах.

Чтобы правильно загрузить mesh-объект, необходимо знать формат данных файла mesh-объекта. Наш простой тип **BasicReaderWriter**, определенный выше, просто считывает данные в виде потока байтов; у него нет информации о том, что эти байтовые данные представляют mesh-объект, и тем более— информации о конкретном формате mesh-объекта, экспортированного из другого приложения. Размещая сведения о mesh-объекте в памяти, вы должны выполнить преобразование.

(Всегда следует стараться упаковывать данные пакета ресурсов в формат, который ближе всего к внутреннему формату представления данных. Это поможет сэкономить время и ресурсы системы.)

Давайте получим байтовые данные из файла mesh-объекта. Предполагается, что формат в нашем примере является пользовательским форматом с расширением .vbo. (Не забудьте, что этот формат не эквивалентен формату OpenGL's VBO.) Каждая вершина сопоставлена типу **BasicVertex**, который структурно определен в коде для средства преобразования obj2vbo. Ниже показана примерная структура данных вершин в файле .vbo:

-   Первые 32 бита (4 байта) потока данных содержат число вершин в mesh-объекте (numVertices) в виде значения uint32.
-   Следующие 32бита (4байта) потока данных содержат число индексов в mesh-объекте (numIndices) в виде значения uint32.
-   Все последующие биты (numVertices \* sizeof(**BasicVertex**)) содержат данные вершин.
-   Заключительные (numIndices \* 16) биты данных содержат информацию об индексе, которая представлена в виде последовательности значений uint16.

Резюме: необходимо знать структуру данных загружаемого mesh-объекта на битовом уровне. Кроме того, необходимо согласовать адресацию объекта. Все Windows8 платформах с прямым порядком байтов.

В следующем примере мы вызываем метод CreateMesh из метода **LoadMeshAsync** для выполнения описанной интерпретации на битовом уровне.

```cpp
task<void> BasicLoader::LoadMeshAsync(
    _In_ Platform::String^ filename,
    _Out_ ID3D11Buffer** vertexBuffer,
    _Out_ ID3D11Buffer** indexBuffer,
    _Out_opt_ uint32* vertexCount,
    _Out_opt_ uint32* indexCount
    )
{
    return m_basicReaderWriter->ReadDataAsync(filename).then([=](const Platform::Array<byte>^ meshData)
    {
        CreateMesh(
            meshData->Data,
            vertexBuffer,
            indexBuffer,
            vertexCount,
            indexCount,
            filename
            );
    });
}
```

**CreateMesh** интерпретирует байтовые данные, загружаемые из файла, и создает буфер вершины и буфер индексов для mesh-объекта путем передачи списков вершин и индексов методу [**ID3D11Device::CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476501) с определением параметра D3D11\_BIND\_VERTEX\_BUFFER или D3D11\_BIND\_INDEX\_BUFFER. Код, который использовался в **BasicLoader**:

```cpp
void BasicLoader::CreateMesh(
    _In_ byte* meshData,
    _Out_ ID3D11Buffer** vertexBuffer,
    _Out_ ID3D11Buffer** indexBuffer,
    _Out_opt_ uint32* vertexCount,
    _Out_opt_ uint32* indexCount,
    _In_opt_ Platform::String^ debugName
    )
{
    // The first 4 bytes of the BasicMesh format define the number of vertices in the mesh.
    uint32 numVertices = *reinterpret_cast<uint32*>(meshData);

    // The following 4 bytes define the number of indices in the mesh.
    uint32 numIndices = *reinterpret_cast<uint32*>(meshData + sizeof(uint32));

    // The next segment of the BasicMesh format contains the vertices of the mesh.
    BasicVertex* vertices = reinterpret_cast<BasicVertex*>(meshData + sizeof(uint32) * 2);

    // The last segment of the BasicMesh format contains the indices of the mesh.
    uint16* indices = reinterpret_cast<uint16*>(meshData + sizeof(uint32) * 2 + sizeof(BasicVertex) * numVertices);

    // Create the vertex and index buffers with the mesh data.

    D3D11_SUBRESOURCE_DATA vertexBufferData = {0};
    vertexBufferData.pSysMem = vertices;
    vertexBufferData.SysMemPitch = 0;
    vertexBufferData.SysMemSlicePitch = 0;
    CD3D11_BUFFER_DESC vertexBufferDesc(numVertices * sizeof(BasicVertex), D3D11_BIND_VERTEX_BUFFER);

    m_d3dDevice->CreateBuffer(
            &vertexBufferDesc,
            &vertexBufferData,
            vertexBuffer
            );
    
    D3D11_SUBRESOURCE_DATA indexBufferData = {0};
    indexBufferData.pSysMem = indices;
    indexBufferData.SysMemPitch = 0;
    indexBufferData.SysMemSlicePitch = 0;
    CD3D11_BUFFER_DESC indexBufferDesc(numIndices * sizeof(uint16), D3D11_BIND_INDEX_BUFFER);
    
    m_d3dDevice->CreateBuffer(
            &indexBufferDesc,
            &indexBufferData,
            indexBuffer
            );
  
    if (vertexCount != nullptr)
    {
        *vertexCount = numVertices;
    }
    if (indexCount != nullptr)
    {
        *indexCount = numIndices;
    }
}
```

Как правило, для каждого mesh-объекта, используемого в игре, создается пара индексов «вершина-буфер». Вы сами определяете, в какую сцену и в какой момент игры загружать mesh-объекты. Если у вас много mesh-объектов, возможно, вы захотите загружать некоторые из них с диска в определенный момент игры, например во время особых заранее определенных состояний загрузки. Для крупных mesh-объектов, например ландшафтов, вы можете загрузить вершины в потоковом режиме из кэша, но эта процедура является более сложной и в данном разделе не рассматривается.

Не забывайте о формате данных ваших вершин! Существует очень много способов представления информации о вершинах для использования в нескольких программах для создания моделей. Кроме того, существует много разных способов представления входного макета информации о вершинах в Direct3D, например списки и полосы треугольников. Дополнительные сведения о представлении вершин см. в разделах [Вводные сведения о буферах Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476898) и [Примитивы](https://msdn.microsoft.com/library/windows/desktop/bb147291).

Теперь давайте рассмотрим загрузку текстур.

### <a name="loading-textures"></a>Загрузка текстур

Текстуры— это самый распространенный ресурс для игр. Именно он составляет большую часть файлов на диске и в памяти. Текстуры, как и mesh-объекты, могут быть представлены в различных форматах, поэтому при загрузке мы преобразуем их в формат, который может использоваться в Direct3D. Текстуры также бывают разных типов и используются для создания различных эффектов. Для улучшения внешнего вида и быстродействия удаленных объектов можно использовать для текстур уровни MIP; карты пятен грязи и освещения используются для создания эффектов в слоях и деталях поверх базовой текстуры; нормальные карты используются при попиксельном расчете эффектов освещения. Типичная сцена в современной игре может содержать тысячи отдельных текстур, и ваш код должен эффективно обрабатывать все эти текстуры.

Для текстур, как и для mesh-объектов, существуют несколько специальных форматов, которые используются для более эффективного использования памяти. Текстуры могут легко поглотить большую часть видео- и системной памяти, поэтому они часто используются в сжатом виде. Необязательно использовать сжатие для игровых текстур. Можно использовать любой алгоритм, но вы должны представить шейдеры с данными в том формате, с которым может работать Direct3D (например, в формате точечного рисунка [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635)).

Direct3D поддерживает алгоритмы сжатия текстур DXT, но может оказаться, что ни один из этих форматов не поддерживается графическим оборудованием на компьютере пользователя игры. DDS-файлы содержат DXT-текстуры (а также другие форматы сжатия текстур) и имеют расширение .dds.

DDS-файл — это двоичный файл, который содержит следующую информацию:

-   Параметр DWORD (контрольное число), содержащий четырехсимвольное кодовое значение «DDS « (0x20534444).

-   Описание данных, содержащихся в файле.

    Данные описываются в описании заголовка с помощью структуры [**DDS\_HEADER**](https://msdn.microsoft.com/library/windows/desktop/bb943982); формат пикселей определяется с помощью структуры [**DDS\_PIXELFORMAT**](https://msdn.microsoft.com/library/windows/desktop/bb943984). Обратите внимание: структуры **DDS\_HEADER** и **DDS\_PIXELFORMAT** заменяют устаревшие структуры DirectDraw 7 DDSURFACEDESC2, DDSCAPS2 и DDPIXELFORMAT **DDS\_HEADER** является двоичным эквивалентом структур DDSURFACEDESC2 и DDSCAPS2. **DDS\_PIXELFORMAT** является двоичным эквивалентом структуры DDPIXELFORMAT.

    ```cpp
    DWORD               dwMagic;
    DDS_HEADER          header;
    ```

    Если значение **dwFlags** в [**DDS\_PIXELFORMAT**](https://msdn.microsoft.com/library/windows/desktop/bb943984) равно DDPF\_FOURCC, а **dwFourCC** — «DX10», будет создана дополнительная структура [**DDS\_HEADER\_DXT10**](https://msdn.microsoft.com/library/windows/desktop/bb943983) для массивов текстур или форматов DXGI, которые нельзя выразить в формате пикселей RGB, например: форматы с плавающей запятой, форматы sRGB и т.д. Если используется структура **DDS\_HEADER\_DXT10**, полное описание данных будет выглядеть следующим образом.

    ```cpp
    DWORD               dwMagic;
    DDS_HEADER          header;
    DDS_HEADER_DXT10    header10;
    ```

-   Указатель на массив байтов, содержащий сведения о главной плоскости.
    ```cpp
    BYTE bdata[]
    ```

-   Указатель на массив байтов, содержащий сведения об остальных плоскостях, например об уровнях MIP, гранях в кубической текстуре, глубин в объемной текстуре. В статьях, ссылки на которые приведены ниже, представлены дополнительные сведения о структуре DDS-файлов для [текстуры](https://msdn.microsoft.com/library/windows/desktop/bb205578), [кубической текстуры](https://msdn.microsoft.com/library/windows/desktop/bb205577) или [объемной текстуры](https://msdn.microsoft.com/library/windows/desktop/bb205579).

    ```cpp
    BYTE bdata2[]
    ```

Экспорт в формат DDS можно выполнить при помощи многих программных средств. Если у вас нет средства для экспорта вашей текстуры в этот формат, подумайте о его создании. Дополнительные сведения о формате DDS и о работе с ним в коде вашей программы см. в статье [Руководство по программированию для DDS](https://msdn.microsoft.com/library/windows/desktop/bb943991). В нашем примере мы будем использовать DDS.

Как и для других типов ресурсов, вы считываете данные из файла как поток байтов. После завершения задач загрузки вызывается лямбда-функция, которая выполняет код (метод **CreateTexture**) для обработки потока байтов и его преобразования в формат, который может использовать Direct3D.

```cpp
task<void> BasicLoader::LoadTextureAsync(
    _In_ Platform::String^ filename,
    _Out_opt_ ID3D11Texture2D** texture,
    _Out_opt_ ID3D11ShaderResourceView** textureView
    )
{
    return m_basicReaderWriter->ReadDataAsync(filename).then([=](const Platform::Array<byte>^ textureData)
    {
        CreateTexture(
            GetExtension(filename) == "dds",
            textureData->Data,
            textureData->Length,
            texture,
            textureView,
            filename
            );
    });
}
```

В предыдущем фрагменте кода лямбда-функция проверяет, имеет ли имя файла расширение «dds». Если имеет, то мы предполагаем, что это DDS-текстура. В противном случае следует использовать API компонента обработки изображений Windows (WIC) для распознавания формата и декодирования данных в формат точечного рисунка. В любом случае результатом будет точечный рисунок [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635) (или ошибка).

```cpp
void BasicLoader::CreateTexture(
    _In_ bool decodeAsDDS,
    _In_reads_bytes_(dataSize) byte* data,
    _In_ uint32 dataSize,
    _Out_opt_ ID3D11Texture2D** texture,
    _Out_opt_ ID3D11ShaderResourceView** textureView,
    _In_opt_ Platform::String^ debugName
    )
{
    ComPtr<ID3D11ShaderResourceView> shaderResourceView;
    ComPtr<ID3D11Texture2D> texture2D;

    if (decodeAsDDS)
    {
        ComPtr<ID3D11Resource> resource;

        if (textureView == nullptr)
        {
            CreateDDSTextureFromMemory(
                m_d3dDevice.Get(),
                data,
                dataSize,
                &resource,
                nullptr
                );
        }
        else
        {
            CreateDDSTextureFromMemory(
                m_d3dDevice.Get(),
                data,
                dataSize,
                &resource,
                &shaderResourceView
                );
        }

        resource.As(&texture2D);
    }
    else
    {
        if (m_wicFactory.Get() == nullptr)
        {
            // A WIC factory object is required in order to load texture
            // assets stored in non-DDS formats.  If BasicLoader was not
            // initialized with one, create one as needed.
            CoCreateInstance(
                    CLSID_WICImagingFactory,
                    nullptr,
                    CLSCTX_INPROC_SERVER,
                    IID_PPV_ARGS(&m_wicFactory));
        }

        ComPtr<IWICStream> stream;
        m_wicFactory->CreateStream(&stream);

        stream->InitializeFromMemory(
                data,
                dataSize);

        ComPtr<IWICBitmapDecoder> bitmapDecoder;
        m_wicFactory->CreateDecoderFromStream(
                stream.Get(),
                nullptr,
                WICDecodeMetadataCacheOnDemand,
                &bitmapDecoder);

        ComPtr<IWICBitmapFrameDecode> bitmapFrame;
        bitmapDecoder->GetFrame(0, &bitmapFrame);

        ComPtr<IWICFormatConverter> formatConverter;
        m_wicFactory->CreateFormatConverter(&formatConverter);

        formatConverter->Initialize(
                bitmapFrame.Get(),
                GUID_WICPixelFormat32bppPBGRA,
                WICBitmapDitherTypeNone,
                nullptr,
                0.0,
                WICBitmapPaletteTypeCustom);

        uint32 width;
        uint32 height;
        bitmapFrame->GetSize(&width, &height);

        std::unique_ptr<byte[]> bitmapPixels(new byte[width * height * 4]);
        formatConverter->CopyPixels(
                nullptr,
                width * 4,
                width * height * 4,
                bitmapPixels.get());

        D3D11_SUBRESOURCE_DATA initialData;
        ZeroMemory(&initialData, sizeof(initialData));
        initialData.pSysMem = bitmapPixels.get();
        initialData.SysMemPitch = width * 4;
        initialData.SysMemSlicePitch = 0;

        CD3D11_TEXTURE2D_DESC textureDesc(
            DXGI_FORMAT_B8G8R8A8_UNORM,
            width,
            height,
            1,
            1
            );

        m_d3dDevice->CreateTexture2D(
                &textureDesc,
                &initialData,
                &texture2D);

        if (textureView != nullptr)
        {
            CD3D11_SHADER_RESOURCE_VIEW_DESC shaderResourceViewDesc(
                texture2D.Get(),
                D3D11_SRV_DIMENSION_TEXTURE2D
                );

            m_d3dDevice->CreateShaderResourceView(
                    texture2D.Get(),
                    &shaderResourceViewDesc,
                    &shaderResourceView);
        }
    }


    if (texture != nullptr)
    {
        *texture = texture2D.Detach();
    }
    if (textureView != nullptr)
    {
        *textureView = shaderResourceView.Detach();
    }
}
```

По завершении работы кода в памяти остается текстура [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635), загруженная из файла изображения. Скорее всего, в вашей игре во всех сценах используется множество текстур (как и mesh-объектов). Если игре необходим регулярный доступ к определенным текстурам, рекомендуется создать кэши для конкретных сцен или уровней вместо того, чтобы загружать все текстуры в момент запуска игры или любого уровня.

(Метод **CreateDDSTextureFromMemory**, который вызывался в приведенном выше примере, можно полностью изучить в разделе [Полный код модуля DDSTextureLoader](complete-code-for-ddstextureloader.md).)

Кроме того, отдельные текстуры или текстурные «оболочки» могут быть сопоставлены определенным полигонам mesh-объекта или плоскостям. Экспорт сведений о таком сопоставлении обычно осуществляется средством, которое использовал художник или дизайнер для создания моделей и текстур. Убедитесь, что при загрузке экспортируемых данных вы захватываете и эту информацию, так как она понадобится для правильного сопоставления текстур и соответствующих плоскостей при выполнении прорисовки фрагмента при помощи построителей текстур.

### <a name="loading-shaders"></a>Загрузка шейдеров

Шейдеры представляют собой скомпилированные файлы на HLSL, которые загружаются в память и вызываются на определенных этапах графического конвейера. Самыми распространенными и необходимыми шейдерами являются шейдеры вершин и построители текстуры, которые обрабатывают — соответственно — отдельные вершины mesh-объектов и пиксели в разных окнах просмотра сцены. HLSL-код используется для преобразования геометрии, применения эффектов освещения и текстур и для дополнительной обработки в процессе прорисовки сцены.

В игре Direct3D может быть несколько различных шейдеров, каждый из которых скомпилирован в отдельный CSO-файл (с расширением .cso, Compiled Shader Object — скомпилированный объект шейдера). Обычно шейдеров не так много, чтобы потребовалась динамическая загрузка; в большинстве случаев вы просто загружаете их при запуске игры или уровня (как, например, шейдер для создания эффекта дождя).

Код в классе **BasicLoader** предоставляет несколько перегрузок для различных шейдеров, в том числе шейдеров вершин, шейдеров геометрии, построителей текстуры и шейдеров поверхностей. В следующем коде для примера показан построитель текстуры. (Полный код можно найти в разделе [Полный код модуля BasicLoader](complete-code-for-basicloader.md).)

```cpp
concurrency::task<void> LoadShaderAsync(
    _In_ Platform::String^ filename,
    _Out_ ID3D11PixelShader** shader
    );

// ...

task<void> BasicLoader::LoadShaderAsync(
    _In_ Platform::String^ filename,
    _Out_ ID3D11PixelShader** shader
    )
{
    return m_basicReaderWriter->ReadDataAsync(filename).then([=](const Platform::Array<byte>^ bytecode)
    {
        
       m_d3dDevice->CreatePixelShader(
                bytecode->Data,
                bytecode->Length,
                nullptr,
                shader);
    });
}

```

В данном примере используется экземпляр **BasicReaderWriter** (**m\_basicReaderWriter**) для чтения имеющегося файла скомпилированного объекта шейдера (.cso) в виде потока байтов. После выполнения этой задачи лямбда-функция вызывает метод [**ID3D11Device::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513) с байтовыми данными, загруженными из файла. Обратный вызов должен устанавливать какой-либо флажок, указывающий на успешное окончание загрузки, и в коде необходимо предусмотреть проверку этого флажка перед запуском шейдера.

Шейдеры вершин немного сложнее. Для шейдеров вершин необходимо также загрузить отдельный входной макет, который будет определять параметры вершины. Для асинхронной загрузки шейдеров вершин вместе с пользовательским входным макетом для вершины можно использовать следующий код. Убедитесь, что этот входной макет может корректно представить информацию о вершинах, которую вы загружаете из mesh-объектов.

Давайте создадим входной макет перед загрузкой шейдера вершин.

```cpp
void BasicLoader::CreateInputLayout(
    _In_reads_bytes_(bytecodeSize) byte* bytecode,
    _In_ uint32 bytecodeSize,
    _In_reads_opt_(layoutDescNumElements) D3D11_INPUT_ELEMENT_DESC* layoutDesc,
    _In_ uint32 layoutDescNumElements,
    _Out_ ID3D11InputLayout** layout
    )
{
    if (layoutDesc == nullptr)
    {
        // If no input layout is specified, use the BasicVertex layout.
        const D3D11_INPUT_ELEMENT_DESC basicVertexLayoutDesc[] =
        {
            { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,  D3D11_INPUT_PER_VERTEX_DATA, 0 },
            { "NORMAL",   0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
            { "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT,    0, 24, D3D11_INPUT_PER_VERTEX_DATA, 0 },
        };

        m_d3dDevice->CreateInputLayout(
                basicVertexLayoutDesc,
                ARRAYSIZE(basicVertexLayoutDesc),
                bytecode,
                bytecodeSize,
                layout);
    }
    else
    {
        m_d3dDevice->CreateInputLayout(
                layoutDesc,
                layoutDescNumElements,
                bytecode,
                bytecodeSize,
                layout);
    }
}
```

В этом конкретном макете каждой вершине соответствуют следующие данные, которые будет обрабатывать шейдер вершин:

-   Положение в трехмерной системе координат модели (X, Y, Z), представленное тройкой 32-разрядных значений с плавающей запятой.
-   Вектор нормали для вершины, также представленный 32-разрядными значениями с плавающей запятой.
-   Значения координат для преобразованной двухмерной текстуры (U, V), представленные в виде пары 32-разрядных значений с плавающей запятой.

Эти элементы входного макета для вершины называются [семантическим элементами HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509647). Они представляют собой набор определенных регистров, которые будут использоваться для передачи данных от скомпилированного объекта шейдера и к этому объекту. Ваш графический конвейер выполняет шейдер вершин один раз для каждой вершины в загружаемом mesh-объекте. Семантические элементы определяют входные (и выходные) данные шейдера вершин по мере его выполнения и предоставляют эти данные для расчета каждой вершины в HLSL-коде построителя текстуры.

Теперь можно загрузить объект шейдера вершин.

```cpp
concurrency::task<void> LoadShaderAsync(
        _In_ Platform::String^ filename,
        _In_reads_opt_(layoutDescNumElements) D3D11_INPUT_ELEMENT_DESC layoutDesc[],
        _In_ uint32 layoutDescNumElements,
        _Out_ ID3D11VertexShader** shader,
        _Out_opt_ ID3D11InputLayout** layout
        );

// ...

task<void> BasicLoader::LoadShaderAsync(
    _In_ Platform::String^ filename,
    _In_reads_opt_(layoutDescNumElements) D3D11_INPUT_ELEMENT_DESC layoutDesc[],
    _In_ uint32 layoutDescNumElements,
    _Out_ ID3D11VertexShader** shader,
    _Out_opt_ ID3D11InputLayout** layout
    )
{
    // This method assumes that the lifetime of input arguments may be shorter
    // than the duration of this task.  In order to ensure accurate results, a
    // copy of all arguments passed by pointer must be made.  The method then
    // ensures that the lifetime of the copied data exceeds that of the task.

    // Create copies of the layoutDesc array as well as the SemanticName strings,
    // both of which are pointers to data whose lifetimes may be shorter than that
    // of this method's task.
    shared_ptr<vector<D3D11_INPUT_ELEMENT_DESC>> layoutDescCopy;
    shared_ptr<vector<string>> layoutDescSemanticNamesCopy;
    if (layoutDesc != nullptr)
    {
        layoutDescCopy.reset(
            new vector<D3D11_INPUT_ELEMENT_DESC>(
                layoutDesc,
                layoutDesc + layoutDescNumElements
                )
            );

        layoutDescSemanticNamesCopy.reset(
            new vector<string>(layoutDescNumElements)
            );

        for (uint32 i = 0; i < layoutDescNumElements; i++)
        {
            layoutDescSemanticNamesCopy->at(i).assign(layoutDesc[i].SemanticName);
        }
    }

    return m_basicReaderWriter->ReadDataAsync(filename).then([=](const Platform::Array<byte>^ bytecode)
    {
       m_d3dDevice->CreateVertexShader(
                bytecode->Data,
                bytecode->Length,
                nullptr,
                shader);

        if (layout != nullptr)
        {
            if (layoutDesc != nullptr)
            {
                // Reassign the SemanticName elements of the layoutDesc array copy to point
                // to the corresponding copied strings. Performing the assignment inside the
                // lambda body ensures that the lambda will take a reference to the shared_ptr
                // that holds the data.  This will guarantee that the data is still valid when
                // CreateInputLayout is called.
                for (uint32 i = 0; i < layoutDescNumElements; i++)
                {
                    layoutDescCopy->at(i).SemanticName = layoutDescSemanticNamesCopy->at(i).c_str();
                }
            }

            CreateInputLayout(
                bytecode->Data,
                bytecode->Length,
                layoutDesc == nullptr ? nullptr : layoutDescCopy->data(),
                layoutDescNumElements,
                layout);   
        }
    });
}

```

В этом коде после считывания байтовых данных для CSO-файла шейдера вершин вызовом метода [**ID3D11Device::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) создается шейдер вершин. После этого в той же лямбда-функции вы создаете входной макет для шейдера.

Другие типы шейдеров, например шейдеры геометрии и поверхности, также могут потребовать определенной настройки. Полный код для разных методов загрузки шейдеров представлен в разделах [Полный код модуля BasicLoader](complete-code-for-basicloader.md) и [Образец загрузки ресурсов Direct3D]( http://go.microsoft.com/fwlink/p/?LinkID=265132).

## <a name="remarks"></a>Примечания

На данном этапе вы должны понимать и уметь создавать или изменять методы для асинхронной загрузки таких распространенных игровых ресурсов, как mesh-объекты, текстуры и скомпилированные шейдеры.

## <a name="related-topics"></a>Связанные разделы

* [Образец загрузки ресурсов Direct3D]( http://go.microsoft.com/fwlink/p/?LinkID=265132)
* [Полный код для BasicLoader](complete-code-for-basicloader.md)
* [Полный код модуля BasicReaderWriter](complete-code-for-basicreaderwriter.md)
* [Полный код модуля DDSTextureLoader](complete-code-for-ddstextureloader.md)

 

 





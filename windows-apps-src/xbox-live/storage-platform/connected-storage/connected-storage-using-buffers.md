---
title: Работа с буферами подключения хранилища
description: Сведения о работе с буферами подключения хранилища.
ms.assetid: 1d9d1b52-4bfe-4cd9-8b80-50ca6c0e9ae1
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, подключенные устройства хранения
ms.localizationpriority: medium
ms.openlocfilehash: 3df95e4807e8d3457143e67eebfb62011bf365cc
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595029"
---
# <a name="working-with-connected-storage-buffers"></a>Работа с буферами подключения хранилища

Подключение API службы хранилища использует **Windows::Storage::Streams::Buffer** экземпляров для передачи данных из приложения. Так как типы WinRT не могут предоставлять необработанных указателей, доступ к данным экземпляра буфера происходит через **DataReader** и **DataWriter** классы. Тем не менее **буфера** также реализует интерфейс COM **IBufferByteAccess**, который дает возможность получить указатель непосредственно на буфер данных.

### <a name="to-get-a-pointer-to-a-buffer-instances-data"></a>Чтобы получить указатель на экземпляр буферов данных

1.  Используйте **восьмому\_приведения** привести экземпляр буфера как **IUnknown**.

```cpp
        IUnknown* unknown = reinterpret_cast<IUnknown*>(buffer);
```

2.  Запрос **IUnknown** интерфейс для **IBufferByteAccess** COM-интерфейса.

```cpp
        Microsoft::WRL::ComPtr<IBufferByteAccess> bufferByteAccess;
        HRESULT hr = unknown->QueryInterface(_uuidof(IBufferByteAccess), &bufferByteAccess);
        if (FAILED(hr))
        return nullptr;
```

3.  Используйте **IBufferByteAccess::Buffer** для получения указателя буфера данных.

```cpp
        byte* bytes = nullptr;
        bufferByteAccess->Buffer(&bytes);
```

Например в следующем образце кода показано, как создать буфер, содержащий текущее системное время. Поскольку буферы отдельное значение емкости и длины бывает необходимо явно задать емкости и длины. По умолчанию длина равна 0.

```cpp
    inline byte* GetBufferData(Windows::Storage::Streams::IBuffer^ buffer)
    {
        using namespace Windows::Storage::Streams;
        IUnknown* unknown = reinterpret_cast<IUnknown*>(buffer);
        Microsoft::WRL::ComPtr<IBufferByteAccess> bufferByteAccess;
        HRESULT hr = unknown->QueryInterface(_uuidof(IBufferByteAccess), &bufferByteAccess);
        if (FAILED(hr))
        return nullptr;
        byte* bytes = nullptr;
        bufferByteAccess->Buffer(&bytes);
        return bytes;
    }

    IBuffer^ WrapRawBuffer( void* ptr, size_t size )
    {
        using namespace Windows::Storage::Streams;

        //uint32 size = sizeof(FILETIME);
        Buffer^ buffer = ref new Buffer(size);
        buffer->Length = size;

        memcpy(GetBufferData(buffer),ptr,size);


        return buffer;

    };
```

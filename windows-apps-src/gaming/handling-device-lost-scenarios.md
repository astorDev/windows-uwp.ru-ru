---
title: Обработка сценариев удаления устройства в Direct3D11
description: В этом разделе объясняется, как повторно создать цепочку интерфейса устройства Direct3D и DXGI при удалении или повторной инициализации графического адаптера.
ms.assetid: 8f905acd-08f3-ff6f-85a5-aaa99acb389a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx 11, потеря устройства
ms.localizationpriority: medium
ms.openlocfilehash: c11bbf7657644fbf616590f50d75d93f62ed993e
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8797694"
---
# <a name="span-iddevgaminghandlingdevice-lostscenariosspanhandle-device-removed-scenarios-in-direct3d-11"></a><span id="dev_gaming.handling_device-lost_scenarios"></span>Обработка сценариев удаления устройства в Direct3D11



В этом разделе объясняется, как повторно создать цепочку интерфейса устройства Direct3D и DXGI при удалении или повторной инициализации графического адаптера.

В приложениях DirectX 9 может возникать условие [потери устройства](https://msdn.microsoft.com/library/windows/desktop/bb174714), при котором устройство D3D переходит в нерабочее состояние. Например, когда полноэкранное приложение Direct3D 9 теряет фокус, устройство Direct3D «теряется» и любые попытки использования потерянного устройства завершаются ошибкой. В Direct3D 11 используются виртуальные интерфейсы графических устройств, что позволяет нескольким программам одновременно использовать одно и то же физическое графическое устройство и исключает возможность появления условий, при которых приложения теряют контроль над устройством Direct3D. Однако доступность графического адаптера все же может измениться. Например:

-   Выполняется обновление графического драйвера.
-   Система переходит с энергосберегающего графического адаптера на производительный графический адаптер.
-   Графическое устройство перестает отвечать и сбрасывается.
-   Физическое подключение или удаление графического адаптера.

При возникновении таких условий DXGI возвращает код ошибки, указывающий, что следует повторно инициализировать устройство Direct3D и воссоздать ресурсы устройства. В этом пошаговом руководстве объясняется, как приложения и игры Direct3D 11 могут обнаружить условия, при которых графический адаптер сбрасывается, удаляется или изменяется, и отреагировать соответствующим образом. Примеры кода взяты из шаблонов приложение DirectX 11 (универсальное приложение Windows), предоставленных в Microsoft Visual Studio2015.

## <a name="instructions"></a>Инструкции

### <a name="spanspanstep-1"></a><span></span>Шаг 1.

Включите проверку ошибки удаленного устройства в цикле отрисовки. Представьте кадр, вызвав метод [**IDXGISwapChain::Present**](https://msdn.microsoft.com/library/windows/desktop/bb174576) (или метод [**Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797)  и т. д.). Затем проверьте, возвратил ли метод ошибку [**DXGI\_ERROR\_DEVICE\_REMOVED**](https://msdn.microsoft.com/library/windows/desktop/bb509553) или **DXGI\_ERROR\_DEVICE\_RESET**.

Прежде всего, шаблон сохраняет HRESULT, возвращенный цепочкой буферов DXGI.

```cpp
HRESULT hr = m_swapChain->Present(1, 0);
```

После выполнения всех необходимых действий для представления кадра шаблон проверяет наличие ошибки удаленного устройства. При необходимости он вызывает метод обработки условия удаленного устройства.

```cpp
// If the device was removed either by a disconnection or a driver upgrade, we
// must recreate all device resources.
if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
{
    HandleDeviceLost();
}
else
{
    DX::ThrowIfFailed(hr);
}
```

### <a name="step-2"></a>Шаг 2.

Также включите проверку на наличие ошибки удаленного устройства в ответ на изменения размера окна. На этом этапе рекомендуется проверять [**DXGI\_ERROR\_DEVICE\_REMOVED**](https://msdn.microsoft.com/library/windows/desktop/bb509553) или **DXGI\_ERROR\_DEVICE\_RESET** по нескольким причинам.

-   Для изменения размера цепочки буферов требуется вызвать базовый адаптер DXGI, который может возвратить ошибку удаленного устройства.
-   Приложение может теперь отображаться на мониторе, подключенном к другому графическому устройству.
-   При удалении или сбросе графического устройства разрешение рабочего стола часто меняется, в результате чего меняется размер окна.

Шаблон проверяет HRESULT, возвращенный методом [**ResizeBuffers**](https://msdn.microsoft.com/library/windows/desktop/bb174577):

```cpp
// If the swap chain already exists, resize it.
HRESULT hr = m_swapChain->ResizeBuffers(
    2, // Double-buffered swap chain.
    static_cast<UINT>(m_d3dRenderTargetSize.Width),
    static_cast<UINT>(m_d3dRenderTargetSize.Height),
    DXGI_FORMAT_B8G8R8A8_UNORM,
    0
    );

if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
{
    // If the device was removed for any reason, a new device and swap chain will need to be created.
    HandleDeviceLost();

    // Everything is set up now. Do not continue execution of this method. HandleDeviceLost will reenter this method 
    // and correctly set up the new device.
    return;
}
else
{
    DX::ThrowIfFailed(hr);
}
```

### <a name="step-3"></a>Шаг 3.

Всякий раз, когда приложение получает ошибку [**DXGI\_ERROR\_DEVICE\_REMOVED**](https://msdn.microsoft.com/library/windows/desktop/bb509553), оно должно повторно инициализировать устройство Direct3D и повторно создать любые зависимые от устройства ресурсы. Освободите все ссылки на ресурсы графического устройства, созданные при использовании предыдущего устройства Direct3D. Эти ресурсы теперь недействительны, и все ссылки на данную цепочку буферов следует освободить до создания новой цепочки.

Метод HandleDeviceLost освобождает цепочку буферов и сообщает компонентам приложения о необходимости освободить ресурсы устройства.

```cpp
m_swapChain = nullptr;

if (m_deviceNotify != nullptr)
{
    // Notify the renderers that device resources need to be released.
    // This ensures all references to the existing swap chain are released so that a new one can be created.
    m_deviceNotify->OnDeviceLost();
}
```

Затем он создает новую цепочку буферов и выполняет повторную инициализацию зависимых от устройства ресурсов, контролируемых классом управления устройством.

```cpp
// Create the new device and swap chain.
CreateDeviceResources();
m_d2dContext->SetDpi(m_dpi, m_dpi);
CreateWindowSizeDependentResources();
```

После повторной установки устройства и цепочки буферов этот метод сообщает компонентам приложения о необходимости повторной инициализации зависимых от устройства ресурсов.

```cpp
// Create the new device and swap chain.
CreateDeviceResources();
m_d2dContext->SetDpi(m_dpi, m_dpi);
CreateWindowSizeDependentResources();

if (m_deviceNotify != nullptr)
{
    // Notify the renderers that resources can now be created again.
    m_deviceNotify->OnDeviceRestored();
}
```

Когда метод HandleDeviceLost завершает работу, элемент управления возвращается в цикл отрисовки, и продолжается отрисовка нового кадра.

## <a name="remarks"></a>Комментарии


### <a name="investigating-the-cause-of-device-removed-errors"></a>Исследование причины ошибок удаленного устройства

Многократное возникновение ошибок удаленного устройства DXGI может означать, что ваш графический код создает недопустимые условия в процессе отрисовки. Также это может указывать на сбой оборудования или ошибку в графическом драйвере. Чтобы проанализировать причину ошибок удаленного устройства, вызовите метод [**ID3D11Device::GetDeviceRemovedReason**](https://msdn.microsoft.com/library/windows/desktop/ff476526) до отсоединения устройства Direct3D. Этот метод возвратит один из шести возможных кодов ошибок DXGI с указанием причины возникновения ошибки удаленного устройства:

-   **DXGI\_ERROR\_DEVICE\_HUNG**: графический драйвер перестал отвечать из-за недопустимого сочетания графических команд, отправляемых приложением. Частое возникновение этой ошибки, скорее всего, указывает на то, что из-за вашего приложения устройство перестает отвечать на запросы, поэтому требуется его отладка.
-   **DXGI\_ERROR\_DEVICE\_REMOVED**: графическое устройство физически удалено или отключено, либо выполнено обновление драйвера. Такая ситуация возникает периодически и является обычной. Ваше приложение или игра должны повторно создать ресурсы устройства, как описано в этом разделе.
-   **DXGI\_ERROR\_DEVICE\_RESET**: сбой графического устройства произошел из-за неправильно заданной команды. Частое возникновение этой ошибки указывает на то, что ваш код отправляет недопустимые команды отрисовки.
-   **DXGI\_ERROR\_DRIVER\_INTERNAL\_ERROR**: графический драйвер обнаружил ошибку и сбросил настройки устройства.
-   **DXGI\_ERROR\_INVALID\_CALL**: приложение предоставило недопустимые данные параметра. Даже однократное возникновение этой ошибки указывает на то, что ваш код привел к возникновению условия удаления устройства и требуется отладка.
-   **S\_OK**: возвращается, когда графическое устройство включается, отключается или его настройки сбрасываются, не вызывая проблем с текущим графическим устройством. Например, этот код ошибки может возвращаться, если приложение использует [платформу WARP](https://msdn.microsoft.com/library/windows/desktop/gg615082) и аппаратный адаптер становится доступен.

В следующем коде будет получен код ошибки [**DXGI\_ERROR\_DEVICE\_REMOVED**](https://msdn.microsoft.com/library/windows/desktop/bb509553), который отобразится в консоли отладки. Вставьте этот код в начало метода HandleDeviceLost:

```cpp
    HRESULT reason = m_d3dDevice->GetDeviceRemovedReason();

#if defined(_DEBUG)
    wchar_t outString[100];
    size_t size = 100;
    swprintf_s(outString, size, L"Device removed! DXGI_ERROR code: 0x%X\n", reason);
    OutputDebugStringW(outString);
#endif
```

Дополнительные сведения см. в разделах [**GetDeviceRemovedReason**](https://msdn.microsoft.com/library/windows/desktop/ff476526) и [**DXGI\_ERROR**](https://msdn.microsoft.com/library/windows/desktop/bb509553).

### <a name="testing-device-removed-handling"></a>Тестирование обработки извлеченного устройства

Командная строка разработчика Visual Studio поддерживает средство dxcap для захвата и воспроизведения событий Direct3D, связанных с диагностикой графики Visual Studio. Вы можете использовать параметр командной строки "-forcetdr", когда приложение работает, что инициирует событие обнаружения таймаута и восстановления GPU, вызовет исключение DXGI\_ERROR\_DEVICE\_REMOVED и позволит проверить код для обработки ошибок.

> **Примечание.** DXCap и вспомогательные DLL-библиотеки устанавливаются в каталоге в system32/syswow64 как часть средств графики для Windows 10, которые больше не распространяются вместе с Windows SDK. Вместо этого они предоставляются с помощью функции «Инструменты для графики по требованию». Это необязательный компонент операционной системы, который необходимо установить для активации и использования инструментов для графики в Windows 10. Дополнительные сведения о том, как установить средств графики для Windows 10 можно найти здесь: <https://msdn.microsoft.com/library/mt125501.aspx#InstallGraphicsTools>

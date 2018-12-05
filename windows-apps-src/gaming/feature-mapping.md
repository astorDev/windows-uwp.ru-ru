---
title: Сопоставление компонентов DirectX9 и API DirectX11
description: Прочитав этот раздел, вы поймете, как компоненты, которые использует ваша игра с Direct3D9, будут перенесены в Direct3D11 и на универсальную платформу Windows (UWP).
ms.assetid: 3aa8a114-4e47-ae0a-9447-88ba324377b8
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx 9, directx 11, перенос
ms.localizationpriority: medium
ms.openlocfilehash: 56bb86706795e773d21e45263f640f9fc0aa596a
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8706392"
---
# <a name="map-directx-9-features-to-directx-11-apis"></a>Сопоставление компонентов DirectX9 и API DirectX11



**Краткий обзор**

-   [Планирование переноса приложений на DirectX](plan-your-directx-port.md)
-   [Важные изменения в Direct3D11 по сравнению с Direct3D9](understand-direct3d-11-1-concepts.md)
-   Сопоставление компонентов


Прочитав этот раздел, вы поймете, как компоненты, которые использует ваша игра с Direct3D9, будут перенесены в Direct3D11 и на универсальную платформу Windows (UWP).

## <a name="mapping-direct3d-9-to-directx-11-apis"></a>Сопоставление Direct3D9 и API DirectX11


[Direct3D](https://msdn.microsoft.com/library/windows/desktop/hh309466) по-прежнему является основой графики DirectX, но после DirectX 9 изменились API.

-   Для установки графических адаптеров используется Microsoft DirectX Graphics Infrastructure (DXGI). Используйте [DXGI](https://msdn.microsoft.com/library/windows/desktop/hh404534) для выбора форматов буферов, создания цепочек буферов, представления кадров и создания общих ресурсов. См. общие сведения о [DXGI](https://msdn.microsoft.com/library/windows/desktop/bb205075).
-   Контекст устройства Direct3D используется для установки состояния конвейера и генерирования команд отрисовки. Во многих наших примерах используется немедленный контекст для прямой отрисовки на устройстве. Direct3D11 также поддерживает многопотоковую отрисовку, в этом случае используются отложенные контексты. См. раздел [Введение в устройство в Direct3D11](https://msdn.microsoft.com/library/windows/desktop/ff476880).
-   Некоторые компоненты устарели, наиболее заметно усовершенствован функциональный конвейер. См. раздел [Устаревшие компоненты](https://msdn.microsoft.com/library/windows/desktop/cc308047).

Полный список компонентов Direct3D11: [Компоненты Direct3D11](https://msdn.microsoft.com/library/windows/desktop/ff476342) и [Компоненты Direct3D11](https://msdn.microsoft.com/library/windows/desktop/hh404562).

## <a name="moving-from-direct2d-9-to-direct2d-11"></a>Переход с Direct2D9 на Direct2D11


[Direct2D (Windows)](https://msdn.microsoft.com/library/windows/desktop/dd370990) по-прежнему является важной частью графики DirectX и Windows. Вы все еще можете использовать Direct2D для отрисовки двухмерных игр и наложений (HUD) на Direct3D.

Direct2D работает на базе Direct3D; двухмерные игры можно реализовать с помощью любого API. Например, при реализации двухмерной игры с помощью Direct3D можно использовать ортогональную проекцию, установить значения Z для управления порядком отрисовки примитивов и использовать построитель текстуры для добавления специальных эффектов.

Так как Direct2D основан на Direct3D, он также использует DXGI и контексты устройств. См. раздел [Обзор API Direct2D](https://msdn.microsoft.com/library/windows/desktop/dd317121).

API [DirectWrite](https://msdn.microsoft.com/library/windows/desktop/dd368038) добавляет поддержку отформатированного текста с помощью Direct2D. См. раздел [Введение в DirectWrite](https://msdn.microsoft.com/library/windows/desktop/dd371554).

## <a name="replace-deprecated-helper-libraries"></a>Замена устаревших вспомогательных библиотек


D3DX и DXUT являются нерекомендуемыми и не могут использоваться играми UWP. Эти вспомогательные библиотеки предоставляли ресурсы для таких задач, как загрузка текстуры и сетки.

-   В пошаговом руководстве [Простой перенос из Direct3D9 на UWP](walkthrough--simple-port-from-direct3d-9-to-11-1.md) показано, как настроить окно, инициализировать Direct3D и выполнить базовую трехмерную отрисовку.
-   В пошаговом руководстве [Простая игра UWP с использованием DirectX](tutorial--create-your-first-uwp-directx-game.md) демонстрируются стандартные задачи, возникающие при программировании игры, включая графику, загрузку файлов, пользовательский интерфейс, элементы управления и звук.
-   Сообщество [DirectX Tool Kit](http://go.microsoft.com/fwlink/p/?LinkID=248929) (Набор инструментов DirectX) предлагает вспомогательные классы для использования с Direct3D11 и приложениями UWP.

## <a name="move-shader-programs-from-fx-to-hlsl"></a>Перенос программ-шейдеров с FX на HLSL


Мы не рекомендуем использовать библиотеку подпрограмм D3DX (D3DX9, D3DX10 и D3DX11), включая эффекты, для UWP. Все игры UWP с DirectX реализует графический конвейер с помощью [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561) без эффектов.

Visual Studio по-прежнему использует FXC для компиляции объектов шейдера. Шейдеры игр UWP компилируются заранее. Байт-код загружается во время выполнения, затем каждый ресурс шейдера привязывается к графическому конвейеру во время соответствующего этапа отрисовки. Шейдеры необходимо переместить в их собственные HLSL-файлы, а методы отрисовки следует реализовать в коде C++.

Чтобы взглянуть на загрузку ресурсов шейдера, см. раздел [Простое портирование с Direct3D9 на UWP](walkthrough--simple-port-from-direct3d-9-to-11-1.md).

В Direct3D11 введена модель шейдера5, которая требует уровня компонентов Direct3D11\_0 (или выше). См. раздел [Компоненты модели5 шейдера HLSL для Direct3D11](https://msdn.microsoft.com/library/windows/desktop/ff471419).

## <a name="replace-xnamath-and-d3dxmath"></a>Замена XNAMath и D3DXMath


Код, использующий XNAMath (или D3DXMath), необходимо перенести на [DirectXMath](https://msdn.microsoft.com/library/windows/desktop/hh437833). DirectXMath включает типы, переносимые на x86, x64 и ARM. См. раздел [Перенос кода из библиотеки XNA Math](https://msdn.microsoft.com/library/windows/desktop/ee418730).

Обратите внимание, что типы чисел с плавающей точкой DirectXMath удобно использовать с шейдерами. Например, [**XMFLOAT4**](https://msdn.microsoft.com/library/windows/desktop/ee419608) и [**XMFLOAT4X4**](https://msdn.microsoft.com/library/windows/desktop/ee419621) обеспечивают удобное выравнивание данных для буферов констант.

## <a name="replace-directsound-with-xaudio2-and-background-audio"></a>Замена DirectSound на XAudio2 (и фоновый звук)


DirectSound не поддерживается для UWP:

-   Используйте [XAudio2](https://msdn.microsoft.com/library/windows/desktop/hh405049), чтобы добавить в игру звуковые эффекты.

##  <a name="replace-directinput-with-xinput-and-uwp-apis"></a>Замена DirectInput на API XInput и UWP


DirectInput не поддерживается для UWP:

-   Используйте обратные вызовы событий ввода [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) для мыши, клавиатуры и сенсорного ввода.
-   Используйте [XInput](https://msdn.microsoft.com/library/windows/desktop/ee417001)1.4 для поддержки игрового устройства управления (и его гарнитуры). Если вы используете общую базу кода для рабочего стола и UWP, вы найдете сведения об обратной совместимости в разделе о [версиях XInput](https://msdn.microsoft.com/library/windows/desktop/hh405051).
-   Подпишитесь на события [**EdgeGesture**](https://msdn.microsoft.com/library/windows/apps/hh701600), если вашей игре нужно использовать панель приложения.

## <a name="use-microsoft-media-foundation-instead-of-directshow"></a>Использование Microsoft Media Foundation вместо DirectShow


DirectShow больше не является частью API DirectX (или API Windows). [Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197) предоставляет видеосодержимое интерфейсу Direct3D посредством общих поверхностей. См. раздел [API видео Direct3D11](https://msdn.microsoft.com/library/windows/desktop/hh447677).

## <a name="replace-directplay-with-networking-code"></a>Замена DirectPlay на сетевой код


Microsoft DirectPlay устарел. Если ваша игра использует сетевые службы, вам нужно предоставить сетевой код, соответствующий требованиям сертификации UWP. Используйте следующие API:

-   [Win32 и COM для приложений UWP apps (сеть) (Windows)](https://msdn.microsoft.com/library/windows/apps/br205759)
-   [**Пространство имен Windows.Networking (Windows)**](https://msdn.microsoft.com/library/windows/apps/br207124)
-   [**Пространство имен Windows.Networking.Sockets (Windows)**](https://msdn.microsoft.com/library/windows/apps/br226960)
-   [**Пространство имен Windows.Networking.Connectivity (Windows)**](https://msdn.microsoft.com/library/windows/apps/br207308)
-   [**Пространство имен Windows.ApplicationModel.Background (Windows)**](https://msdn.microsoft.com/library/windows/apps/br224847)

Следующие статьи помогут вам добавить сетевые возможности и объявить поддержку сети в манифесте пакета приложения.

-   [Подключение с помощью сокетов (приложения UWP на C#, VB, C++ и XAML) (Windows)](https://msdn.microsoft.com/library/windows/apps/xaml/hh452976)
-   [Подключение с помощью WebSockets (приложения UWP на C#, VB, C++ и XAML) (Windows)](https://msdn.microsoft.com/library/windows/apps/xaml/hh994396)
-   [Подключение к веб-службам (приложения UWP на C#, VB, C++ и XAML) (Windows)](https://msdn.microsoft.com/library/windows/apps/xaml/hh761504)
-   [Основы работы с сетями](https://msdn.microsoft.com/library/windows/apps/mt280233)

Обратите внимание, что все приложения UWP (включая игры) используют специфические типы фоновых задач для поддержки подключения, когда приложение приостановлено. Если вашей игре необходимо поддерживать подключенное состояние во время приостановки, см. раздел [Основы работы в сети](https://msdn.microsoft.com/library/windows/apps/mt280233).

## <a name="function-mapping"></a>Сопоставление функций


Используйте следующую таблицу, чтобы преобразовать код из Direct3D9 в Direct3D11. Это также поможет понять разницу между устройством и контекстом устройства.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Direct3D9</th>
<th align="left">Эквивалент в Direct3D11</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174336">IDirect3DDevice9</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/dn280493">ID3D11Device2</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/dn280498">ID3D11DeviceContext2</a></p>
<p>Этапы графического конвейера описаны в статье <a href="https://msdn.microsoft.com/library/windows/desktop/ff476882">Графический конвейер</a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174300">IDirect3D9</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/hh404556">IDXGIFactory2</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/hh404537">IDXGIAdapter2</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/dn280345">IDXGIDevice3</a></p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174423">IDirect3DDevice9::Present</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/hh446797">IDXGISwapChain1::Present1</a></p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174472">IDirect3DDevice9::TestCooperativeLevel</a></p></td>
<td align="left"><p>Вызовите <a href="https://msdn.microsoft.com/library/windows/desktop/hh446797">IDXGISwapChain1::Present1</a> с установленным флагом DXGI_PRESENT_TEST.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174322">IDirect3DBaseTexture9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205909">IDirect3DTexture9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174329">IDirect3DCubeTexture9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205941">IDirect3DVolumeTexture9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205865">IDirect3DIndexBuffer9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205915">IDirect3DVertexBuffer9</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476351">ID3D11Buffer</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476633">ID3D11Texture1D</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476635">ID3D11Texture2D</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476637">ID3D11Texture3D</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476628">ID3D11ShaderResourceView</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476582">ID3D11RenderTargetView</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476377">ID3D11DepthStencilView</a></p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205922">IDirect3DVertexShader9</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205869">IDirect3DPixelShader9</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476641">ID3D11VertexShader</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476576">ID3D11PixelShader</a></p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205919">IDirect3DVertexDeclaration9</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476575">ID3D11InputLayout</a></p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205805">IDirect3DDevice9::SetRenderState</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205806">IDirect3DDevice9::SetSamplerState</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/hh404571">ID3D11BlendState1</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476375">ID3D11DepthStencilState</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/hh446828">ID3D11RasterizerState1</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476588">ID3D11SamplerState</a></p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174369">IDirect3DDevice9::DrawIndexedPrimitive</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174371">IDirect3DDevice9::DrawPrimitive</a></p></td>
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476407">ID3D11DeviceContext::Draw</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/ff476409">ID3D11DeviceContext::DrawIndexed</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb173566">ID3D11DeviceContext::DrawIndexedInstanced</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb173567">ID3D11DeviceContext::DrawInstanced</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb173590">ID3D11DeviceContext::IASetPrimitiveTopology</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb173564">ID3D11DeviceContext::DrawAuto</a></p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174350">IDirect3DDevice9::BeginScene</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174375">IDirect3DDevice9::EndScene</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174372">IDirect3DDevice9::DrawPrimitiveUP</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174370">IDirect3DDevice9::DrawIndexedPrimitiveUP</a></p></td>
<td align="left"><p>Нет прямого эквивалента</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174470">IDirect3DDevice9::ShowCursor</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174429">IDirect3DDevice9::SetCursorPosition</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174430">IDirect3DDevice9::SetCursorProperties</a></p></td>
<td align="left"><p>Используйте стандартные API курсора.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174425">IDirect3DDevice9::Reset</a></p></td>
<td align="left"><p>LOST (состояние устройства) и POOL_MANAGED более не существуют. <a href="https://msdn.microsoft.com/library/windows/desktop/hh446797">IDXGISwapChain1::Present1</a> может завершиться сбоем и возвратить значение <a href="https://msdn.microsoft.com/library/windows/desktop/bb509553">DXGI_ERROR_DEVICE_REMOVED</a>.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174373">IDirect3DDevice9:DrawRectPatch</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174374">IDirect3DDevice9:DrawTriPatch</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174421">IDirect3DDevice9:LightEnable</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174422">IDirect3DDevice9:MultiplyTransform</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205798">IDirect3DDevice9:SetLight</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174437">IDirect3DDevice9:SetMaterial</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174438">IDirect3DDevice9:SetNPatchMode</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174463">IDirect3DDevice9:SetTransform</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174433">IDirect3DDevice9:SetFVF</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174462">IDirect3DDevice9:SetTextureStageState</a></p></td>
<td align="left"><p>Конвейер фиксированной функции устарел.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174308">IDirect3DDevice9:CheckDepthStencilMatch</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174309">IDirect3DDevice9:CheckDeviceFormat</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb174320">IDirect3DDevice9:GetDeviceCaps</a></p>
<p><a href="https://msdn.microsoft.com/library/windows/desktop/bb205859">IDirect3DDevice9:ValidateDevice</a></p></td>
<td align="left"><p>Биты возможностей заменены уровнями компонентов. Только некоторые сценарии использования форматов и компонентов являются необязательными для любого отдельно взятого уровня компонентов. Это можно проверить с помощью <a href="https://msdn.microsoft.com/library/windows/desktop/ff476497">ID3D11Device::CheckFeatureSupport</a> и <a href="https://msdn.microsoft.com/library/windows/desktop/bb173536">ID3D11Device::CheckFormatSupport</a>.</p></td>
</tr>
</tbody>
</table>

 

## <a name="surface-format-mapping"></a>Сопоставление форматов поверхностей


Используйте следующую таблицу, чтобы преобразовать форматы Direct3D9 в форматы DXGI.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Формат Direct3D 9</th>
<th align="left">Формат Direct3D 11</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>D3DFMT_UNKNOWN</p></td>
<td align="left"><p>DXGI_FORMAT_UNKNOWN</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_R8G8B8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A8R8G8B8</p></td>
<td align="left"><p>DXGI_FORMAT_B8G8R8A8_UNORM</p>
<p>DXGI_FORMAT_B8G8R8A8_UNORM_SRGB</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_X8R8G8B8</p></td>
<td align="left"><p>DXGI_FORMAT_B8G8R8X8_UNORM</p>
<p>DXGI_FORMAT_B8G8R8X8_UNORM_SRGB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_R5G6B5</p></td>
<td align="left"><p>DXGI_FORMAT_B5G6R5_UNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_X1R5G5B5</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A1R5G5B5</p></td>
<td align="left"><p>DXGI_FORMAT_B5G5R5A1_UNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A4R4G4B4</p></td>
<td align="left"><p>DXGI_FORMAT_B4G4R4A4_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_R3G3B2</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A8</p></td>
<td align="left"><p>DXGI_FORMAT_A8_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A8R3G3B2</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_X4R4G4B4</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A2B10G10R10</p></td>
<td align="left"><p>DXGI_FORMAT_R10G10B10A2</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A8B8G8R8</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8B8A8_UNORM</p>
<p>DXGI_FORMAT_R8G8B8A8_UNORM_SRGB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_X8B8G8R8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_G16R16</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A2R10G10B10</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A16B16G16R16</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A8P8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_P8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_L8</p></td>
<td align="left"><p>DXGI_FORMAT_R8_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  использование Фиксировано .r в шейдере, чтобы дублировать красный цвет в другие компоненты, чтобы получить поведение Direct3D 9.
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A8L8</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  используйте Фиксировано .rrrg в шейдере, чтобы дублировать красный цвет, переместить зеленый цвет в альфа-компоненты поведение Direct3D 9 и.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A4L4</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_V8U8</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8_SNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_L6V5U5</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_X8L8V8U8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_Q8W8V8U8</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8B8A8_SNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_V16U16</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_SNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_W11V11U10</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A2W10V10U10</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_UYVY</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_R8G8_B8G8</p></td>
<td align="left"><p>DXGI_FORMAT_G8R8_G8B8_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  в Direct3D 9 данные было увеличено с 255.0f, но это может быть обработано шейдером.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_YUY2</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_G8R8_G8B8</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8_B8G8_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  в Direct3D 9 данные было увеличено с 255.0f, но это может быть обработано шейдером.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_DXT1</p></td>
<td align="left"><p>DXGI_FORMAT_BC1_UNORM & DXGI_FORMAT_BC1_UNORM_SRGB</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_DXT2</p></td>
<td align="left"><p>DXGI_FORMAT_BC1_UNORM & DXGI_FORMAT_BC1_UNORM_SRGB</p>
<div class="alert">
<strong>Примечание</strong>  DXT1 и DXT2 — же с точки зрения API и оборудования. Единственная разница заключается в том, использовалось ли предварительно умноженное альфа-значение, которое может отследить приложение и которому не требуется отдельный формат.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_DXT3</p></td>
<td align="left"><p>DXGI_FORMAT_BC2_UNORM & DXGI_FORMAT_BC2_UNORM_SRGB</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_DXT4</p></td>
<td align="left"><p>DXGI_FORMAT_BC2_UNORM & DXGI_FORMAT_BC2_UNORM_SRGB</p>
<div class="alert">
<strong>Примечание</strong>  DXT3 и DXT4 — же с точки зрения API и оборудования. Единственная разница заключается в том, использовалось ли предварительно умноженное альфа-значение, которое может отследить приложение и которому не требуется отдельный формат.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_DXT5</p></td>
<td align="left"><p>DXGI_FORMAT_BC3_UNORM & DXGI_FORMAT_BC3_UNORM_SRGB</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_D16 & D3DFMT_D16_LOCKABLE</p></td>
<td align="left"><p>DXGI_FORMAT_D16_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_D32</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_D15S1</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_D24S8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_D24X8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_D24X4S4</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_D16</p></td>
<td align="left"><p>DXGI_FORMAT_D16_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_D32F_LOCKABLE</p></td>
<td align="left"><p>DXGI_FORMAT_D32_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_D24FS8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_S1D15</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_S8D24</p></td>
<td align="left"><p>DXGI_FORMAT_D24_UNORM_S8_UINT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_X8D24</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_X4S4D24</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_L16</p></td>
<td align="left"><p>DXGI_FORMAT_R16_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  использование Фиксировано .r в шейдере, чтобы дублировать красный цвет в другие компоненты поведение D3D9.
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_INDEX16</p></td>
<td align="left"><p>DXGI_FORMAT_R16_UINT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_INDEX32</p></td>
<td align="left"><p>DXGI_FORMAT_R32_UINT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_Q16W16V16U16</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_SNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_MULTI2_ARGB8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_R16F</p></td>
<td align="left"><p>DXGI_FORMAT_R16_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_G16R16F</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_A16B16G16R16F</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_R32F</p></td>
<td align="left"><p>DXGI_FORMAT_R32_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_G32R32F</p></td>
<td align="left"><p>DXGI_FORMAT_R32G32_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DFMT_A32B32G32R32F</p></td>
<td align="left"><p>DXGI_FORMAT_R32G32B32A32_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DFMT_CxV8U8</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_FLOAT1</p></td>
<td align="left"><p>DXGI_FORMAT_R32_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_FLOAT2</p></td>
<td align="left"><p>DXGI_FORMAT_R32G32_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_FLOAT3</p></td>
<td align="left"><p>DXGI_FORMAT_R32G32B32_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_FLOAT4</p></td>
<td align="left"><p>DXGI_FORMAT_R32G32B32A32_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPED3DCOLOR</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_UBYTE4</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8B8A8_UINT</p>
<div class="alert">
<strong>Примечание</strong>  шейдер получает значения UINT, но если Direct3D 9 стиле целых чисел с плавающей точкой (0, 0f, 1.0f … 255.f), UINT можно просто преобразовать в тип float32 в шейдере.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_SHORT2</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_SINT</p>
<div class="alert">
<strong>Примечание</strong>  шейдер получает значения SINT, но если чисел с плавающей точкой неотъемлемой частью стиле Direct3D 9, SINT можно просто преобразовать в тип float32 в шейдере.
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_SHORT4</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_SINT</p>
<div class="alert">
<strong>Примечание</strong>  шейдер получает значения SINT, но если чисел с плавающей точкой неотъемлемой частью стиле Direct3D 9, SINT можно просто преобразовать в тип float32 в шейдере.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_UBYTE4N</p></td>
<td align="left"><p>DXGI_FORMAT_R8G8B8A8_UNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_SHORT2N</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_SNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_SHORT4N</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_SNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_USHORT2N</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_UNORM</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_USHORT4N</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_UNORM</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_UDEC3</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_DEC3N</p></td>
<td align="left"><p>Отсутствует</p></td>
</tr>
<tr class="even">
<td align="left"><p>D3DDECLTYPE_FLOAT16_2</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16_FLOAT</p></td>
</tr>
<tr class="odd">
<td align="left"><p>D3DDECLTYPE_FLOAT16_4</p></td>
<td align="left"><p>DXGI_FORMAT_R16G16B16A16_FLOAT</p></td>
</tr>
<tr class="even">
<td align="left"><p>FourCC 'ATI1'</p></td>
<td align="left"><p>DXGI_FORMAT_BC4_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  требует уровня компонентов 10.0 или более поздней версии
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>FourCC 'ATI2'</p></td>
<td align="left"><p>DXGI_FORMAT_BC5_UNORM</p>
<div class="alert">
<strong>Примечание</strong>  требует уровня компонентов 10.0 или более поздней версии
</div>
<div>
 
</div></td>
</tr>
</tbody>
</table>

 

 

 





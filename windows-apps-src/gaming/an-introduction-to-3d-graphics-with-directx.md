---
title: Простая трехмерная графика для игр DirectX
description: Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики.
ms.assetid: 2989c91f-7b45-7377-4e83-9daa0325e92e
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, графика
ms.localizationpriority: medium
ms.openlocfilehash: 556c5c74e5c8284e56047b4b8a9b2c2bf9c0155c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369075"
---
# <a name="basic-3d-graphics-for-directx-games"></a>Простая трехмерная графика для игр DirectX



Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики.

**Цель:** Узнайте, как программировать приложения трехмерной графики.

## <a name="prerequisites"></a>предварительные требования


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

**Общее время:** 30 минут.

## <a name="where-to-go-from-here"></a>Дополнительные ресурсы


Здесь мы говорим о разработке трехмерной графики с DirectX и C++\\Cx. В этом учебнике, который состоит из пяти частей, представлен обзор API [Direct3D](https://docs.microsoft.com/windows/desktop/direct3d), а также концепции и код, используемые во многих других примерах с DirectX. В учебнике последовательно описываются различные аспекты от настройки DirectX для приложения UWP на языке C++ до создания текстуры примитивов и добавления эффектов.

> **Примечание**  этот учебник использует правостороннюю систему координат с векторами столбца. Во многих примерах и приложениях DirectX применяется левая система координат с векторами строк. Чтобы получить более полное графическое математическое решение, поддерживающее левую систему координат с векторами строк, попробуйте воспользоваться [DirectXMath](https://docs.microsoft.com/windows/desktop/dxmath/directxmath-portal). Дополнительные сведения см. в разделе [Использование DirectXMath с Direct3D](https://docs.microsoft.com/windows/desktop/dxmath/pg-xnamath-migration-d3dx).

 

Здесь показаны следующие действия:

-   инициализация интерфейсов [Direct3D](https://docs.microsoft.com/windows/desktop/direct3d) с использованием среды выполнения Windows;
-   применение операций вершинного шейдера;
-   настройка геометрии;
-   растеризация сцены (сжатие трехмерной сцены в двухмерную проекцию);
-   удаление скрытых поверхностей;

> **Примечание**  

 

Затем мы создадим устройство Direct3D, цепочку буферов и представление однобуферной обработки и выведем сформированное изображение на экран.

[Краткое руководство: Настройка ресурсов DirectX и отображения изображения](setting-up-directx-resources.md)

## <a name="related-topics"></a>См. также


* [Direct3D 11 графики](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)
* [DXGI](https://docs.microsoft.com/windows/desktop/direct3ddxgi/dx-graphics-dxgi)
* [HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl)

 

 





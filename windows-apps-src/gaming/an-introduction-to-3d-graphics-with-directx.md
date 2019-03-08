---
title: Простая трехмерная графика для игр DirectX
description: Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики.
ms.assetid: 2989c91f-7b45-7377-4e83-9daa0325e92e
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, графика
ms.localizationpriority: medium
ms.openlocfilehash: 5dbdf6072f57d12d424f0787cfa2e8993a1624af
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57621799"
---
# <a name="basic-3d-graphics-for-directx-games"></a>Простая трехмерная графика для игр DirectX



Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики.

**Цель:** Узнайте, как программировать приложения трехмерной графики.

## <a name="prerequisites"></a>Предварительные условия


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

**Общее время:** 30 минут.

## <a name="where-to-go-from-here"></a>Дополнительные ресурсы


Здесь мы говорим о разработке трехмерной графики с DirectX и C++\\Cx. В этом учебнике, который состоит из пяти частей, представлен обзор API [Direct3D](https://msdn.microsoft.com/library/windows/desktop/hh309466), а также концепции и код, используемые во многих других примерах с DirectX. В учебнике последовательно описываются различные аспекты от настройки DirectX для приложения UWP на языке C++ до создания текстуры примитивов и добавления эффектов.

> **Примечание**  этот учебник использует правостороннюю систему координат с векторами столбца. Во многих примерах и приложениях DirectX применяется левая система координат с векторами строк. Чтобы получить более полное графическое математическое решение, поддерживающее левую систему координат с векторами строк, попробуйте воспользоваться [DirectXMath](https://msdn.microsoft.com/library/windows/desktop/hh437833). Дополнительные сведения см. в разделе [Использование DirectXMath с Direct3D](https://msdn.microsoft.com/library/windows/desktop/ff729728#Use_DXMath_with_D3D).

 

Здесь показаны следующие действия:

-   инициализация интерфейсов [Direct3D](https://msdn.microsoft.com/library/windows/desktop/hh309466) с использованием среды выполнения Windows;
-   применение операций вершинного шейдера;
-   настройка геометрии;
-   растеризация сцены (сжатие трехмерной сцены в двухмерную проекцию);
-   удаление скрытых поверхностей;

> **Примечание**  

 

Затем мы создадим устройство Direct3D, цепочку буферов и представление однобуферной обработки и выведем сформированное изображение на экран.

[Краткое руководство: Настройка ресурсов DirectX и отображения изображения](setting-up-directx-resources.md)

## <a name="related-topics"></a>Статьи по теме


* [Direct3D 11 графики](https://msdn.microsoft.com/library/windows/desktop/ff476080)
* [DXGI](https://msdn.microsoft.com/library/windows/desktop/hh404534)
* [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561)

 

 





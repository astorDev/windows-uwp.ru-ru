---
author: mtoepke
title: "Простая трехмерная графика для игр DirectX"
description: "Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики."
ms.assetid: 2989c91f-7b45-7377-4e83-9daa0325e92e
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, directx, графика"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 2ac11ce220bc1c62c81df12fbf9c2a41fda1d940
ms.lasthandoff: 02/07/2017

---

# <a name="basic-3d-graphics-for-directx-games"></a>Простая трехмерная графика для игр DirectX


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Здесь мы покажем, как использовать программирование в DirectX для реализации базовых принципов трехмерной графики.

**Цель:** Создание трехмерного графического приложения.

## <a name="prerequisites"></a>Предварительные требования


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

**Общее время выполнения:** 30 минут.

## <a name="where-to-go-from-here"></a>Дополнительные ресурсы


Здесь мы поговорим о разработке трехмерной графики с помощью DirectX и C++\\Cx. В этом учебнике, который состоит из пяти частей, представлен обзор API [Direct3D](https://msdn.microsoft.com/library/windows/desktop/hh309466), а также концепции и код, используемые во многих других примерах с DirectX. В учебнике последовательно описываются различные аспекты от настройки DirectX для приложения UWP на языке C++ до создания текстуры примитивов и добавления эффектов.

> **Примечание**. В этом учебнике используется правая система координат с векторами столбцов. Во многих примерах и приложениях DirectX применяется левая система координат с векторами строк. Чтобы получить более полное графическое математическое решение, поддерживающее левую систему координат с векторами строк, попробуйте воспользоваться [DirectXMath](https://msdn.microsoft.com/library/windows/desktop/hh437833). Дополнительные сведения см. в разделе [Использование DirectXMath с Direct3D](https://msdn.microsoft.com/library/windows/desktop/ff729728#Use_DXMath_with_D3D).

 

Здесь показаны следующие действия:

-   инициализация интерфейсов [Direct3D](https://msdn.microsoft.com/library/windows/desktop/hh309466) с использованием среды выполнения Windows;
-   применение операций вершинного шейдера;
-   настройка геометрии;
-   растеризация сцены (сжатие трехмерной сцены в двухмерную проекцию);
-   удаление скрытых поверхностей;

> **Примечание**  
Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

Затем мы создадим устройство Direct3D, цепочку буферов и представление однобуферной обработки и выведем сформированное изображение на экран.

[Краткое руководство: настройка ресурсов DirectX и отображение изображения](setting-up-directx-resources.md)

## <a name="related-topics"></a>Связанные темы


* [Графика Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476080)
* [DXGI](https://msdn.microsoft.com/library/windows/desktop/hh404534)
* [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561)

 

 






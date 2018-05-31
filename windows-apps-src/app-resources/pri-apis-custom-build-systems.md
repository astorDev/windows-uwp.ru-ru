---
author: stevewhims
Description: With the package resource indexing (PRI) APIs, you can develop a custom build system for your UWP app's resources. The build system will be able to create, version, and dump PRI files to whatever level of complexity your UWP app needs.
title: API-интерфейса индексирования ресурсов пакета (PRI) и пользовательские системы сборки
template: detail.hbs
ms.author: stwhi
ms.date: 02/20/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 8947f9305829e5045a418ac2fdd2d6c327acb2a1
ms.sourcegitcommit: 346b5c9298a6e9e78acf05944bfe13624ea7062e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "1707355"
---
# <a name="package-resource-indexing-pri-apis-and-custom-build-systems"></a>API-интерфейса индексирования ресурсов пакета (PRI) и пользовательские системы сборки
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

С помощью [API-интерфейсов индексирования ресурсов пакета (PRI)](https://msdn.microsoft.com/library/windows/desktop/mt845690) можно создать настраиваемую систему сборки для ресурсов приложения UWP. Система сборка сможет создавать файлы индекса ресурсов пакета (PRI), управлять версиями и создавать дампы файлов (в формате XML) пакетов (PRI) любого уровня сложности, требуемого приложению UWP. Если вы используете систему сборки, которая применяет средство командной строки MakePri.exe (см. раздел [Компиляция ресурсов вручную с помощью MakePri.exe](makepri-exe-command-options.md)), то для повышения производительности и улучшения управления рекомендуется вызывать API-интерфейсы PRI вместо MakePri.exe.

API-интерфейсы PRI появились в Windows SDK для Windows 10 версии 1803. API-интерфейсы принимает вид интерфейсов Win32 для Windows, то есть вы можете вызывать их несколькими способами. Их можно вызывать напрямую из приложения Win32 или их помощью [метода вызова платформы](/dotnet/framework/interop/consuming-unmanaged-dll-functions?branch=live) из приложения .NET или даже приложения UWP.

В сценариях в этом разделе показаны вызовы API-интерфейсов PRI из проекта консольного приложения Win32 Visual C++ для Windows. Подробнее см. в разделе [Система управления ресурсами](resource-management-system.md).

Ограничение на размер PRI-файла— 64КБ.

> [!NOTE]
> Это ограничение вряд ли станет проблемой, так как, скорее всего, вы не будете отправлять приложение системы сборки в Microsoft Store. Но если вы решили создать собственную систему сборки в виде приложение UWP, то это будет необычное приложение UWP, которое невозможно отправить в Microsoft Store. Это связано с тем, что приложение UWP, которое использует методы вызова платформы, не пройдет сертификацию Microsoft Store. Обратите внимание, что в этом случае вызовы платформы *будут существовать только в вашей системе сборки*, но *не* в вашем приложении UWP (для которого вы создаете PRI-файлы).

## <a name="scenario-walkthroughs"></a>Пошаговые руководства для сценариев
|Статья|Описание|
|-|-|
|[Сценарий 1. Создание PRI-файла из строковых ресурсов и файлов ресурсов](pri-apis-scenario-1.md)|В этом сценарии мы создаем новое приложение для нашей системы сборки. Мы создадим индексатор ресурсов и добавим в него строки и другие типы ресурсов. Затем мы создадим PRI-файл и его дамп.|

## <a name="important-apis"></a>Важные API
* [Справочник по индексированию ресурсов пакетов (PRI)](https://msdn.microsoft.com/library/windows/desktop/mt845690)

## <a name="related-topics"></a>Статьи по теме
* [Компиляция ресурсов вручную с помощью MakePri.exe](makepri-exe-command-options.md)
* [Использование неуправляемых функций DLL](/dotnet/framework/interop/consuming-unmanaged-dll-functions?branch=live)
* [Система управления ресурсами](resource-management-system.md)

---
author: stevewhims
Description: At build time, the Resource Management System creates an index of all the different variants of the resources that are packaged up with your app. At run-time, the system detects the user and machine settings that are in effect and loads the resources that are the best match for those settings.
title: Система управления ресурсами
template: detail.hbs
ms.author: stwhi
ms.date: 10/20/2017
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: b80eda57ff700d732ba2402582ed6402acca4fc6
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5827290"
---
# <a name="resource-management-system"></a>Система управления ресурсами
Система управления ресурсами имеет функции, работающие во время сборки и во время выполнения. Во время построения система создает индекс всех различных вариантов ресурсов, которые упакованы с вашим приложением. Этот индекс называется индексом ресурсов пакета или PRI и также включается в пакет вашего приложения. Во время выполнения система определяет текущие параметры пользователя и компьютера, проверяет информацию в PRI и автоматически загружает ресурсы, которые лучше всего подходят для этих параметров.

## <a name="package-resource-index-pri-file"></a>Файл индекса ресурсов пакета (PRI)
Каждый пакет приложения должен содержать двоичный индекс ресурсов приложения. Этот индекс создается во время сборки и содержится в одном или нескольких файлах индекса ресурсов пакета (PRI).

- Файл PRI содержит фактические строковые ресурсы, а также индексированный набор путей к различным файлам, содержащимся в пакете.
- Пакет обычно включает один PRI-файл для каждого языка, который называется resources.pri.
- Файл resources.pri, расположенный в корне каждого пакета, автоматически загружается при создании экземпляра [**ResourceManager**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live).
- Создавать и выгружать PRI-файлы можно с помощью [MakePRI.exe](compile-resources-manually-with-makepri.md).
- Для разработки приложений MakePRI.exe обычно не требуется, так как он уже интегрирован в рабочий процесс компиляции Visual Studio. И Visual Studio поддерживает редактирование файлов PRI в выделенном пользовательском интерфейсе. Однако ваши локализаторы и средства, которые они используют, могут опираться на MakePRI.exe.
- Каждый PRI-файл содержит именованный набор ресурсов — карту ресурсов. Во время загрузки PRI-файла из пакета имя карты ресурсов сопоставляется с именем идентификатора пакета.
- PRI-файлы содержат только данные, поэтому они не используют переносимый исполняемый формат (PE). Эти файлы специально предназначены для хранения данных в формате ресурсов для Windows. Они заменяют ресурсы, которые в модели приложений Microsoft Win32 содержатся в библиотеках DLL.
- Ограничение на размер PRI-файла— 64КБ.

## <a name="uwp-api-access-to-app-resources"></a>Доступ к ресурсам приложения через API UWP

### <a name="basic-functionality-resourceloader"></a>Базовые функции (ResourceLoader)
Самый простой способ программного доступа к ресурсам приложения — использование пространства имен [**Windows.ApplicationModel.Resources**](/uwp/api/windows.applicationmodel.resources?branch=live) и класса [**ResourceLoader**](/uwp/api/windows.applicationmodel.resources.resourceloader?branch=live). **ResourceLoader** обеспечивает базовый доступ к строковым ресурсам из набора файлов ресурсов, библиотек, на которые имеются ссылки, или других пакетов.

### <a name="advanced-functionality-resourcemanager"></a>Расширенные функции (ResourceManager)
Класс [**ResourceManager**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live) (в пространстве имен [**Windows.ApplicationModel.Resources.Core**](/uwp/api/windows.applicationmodel.resources.core?branch=live)) предоставляет дополнительные сведения о ресурсах, такие как перечисление и проверка. Класс **ResourceLoader** такую возможность не предоставляет.

Объект [**NamedResource**](/uwp/api/windows.applicationmodel.resources.core.namedresource?branch=live) представляет собой отдельный логический ресурс с несколькими языковыми или иными квалифицированными вариантами. Он описывает логическое представление ресурса с помощью строки идентификатора ресурса, такой как `Header1`, или имени файла ресурсов, например `logo.jpg`.

Объект [**ResourceCandidate**](/uwp/api/windows.applicationmodel.resources.core.resourcecandidate?branch=live) представляет собой одно конкретное значение ресурса и его квалификаторы, например строку Hello, world для английского языка или logo.scale-100.jpg как квалифицированный ресурс изображения, относящийся к разрешению **scale-100**.

Доступные приложению ресурсы хранятся в иерархических коллекциях, доступ к которым можно получить с помощью объекта [**ResourceMap**](/uwp/api/windows.applicationmodel.resources.core.resourcemap?branch=live). Класс **ResourceManager** предоставляет доступ к разным экземплярам **ResourceMap** верхнего уровня, используемым приложением, которые соответствуют разным пакетам приложения. Значение [**MainResourceMap**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager.MainResourceMap) соответствует карте ресурсов для текущего пакета приложения, за исключением пакетов платформ, на которые имеются ссылки. Каждая карта **ResourceMap** носит имя пакета, которое указано в манифесте пакета. В **ResourceMap** существуют поддеревья (см. [**ResourceMap.GetSubtree**](/uwp/api/windows.applicationmodel.resources.core.resourcemap.getsubtree?branch=live)), которые содержат дополнительные объекты **NamedResource**. Поддеревья обычно соответствуют файлам ресурсов, которые содержат ресурс. Дополнительные сведения см. в разделах [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](localize-strings-ui-manifest.md) и [Загрузка изображений и ресурсов, адаптированных по масштабированию, теме, высокой контрастности и другим аспектам](images-tailored-for-scale-theme-contrast.md).

Вот пример.

```csharp
// using Windows.ApplicationModel.Resources.Core;
ResourceMap resourceMap =  ResourceManager.Current.MainResourceMap.GetSubtree("Resources");
ResourceContext resourceContext = ResourceContext.GetForCurrentView()
var str = resourceMap.GetValue("String1", resourceContext).ValueAsString;
```

**Примечание**. Идентификатор ресурса обрабатывается как фрагмент универсального кода ресурса (URI) с учетом семантики URI. Например, `GetValue("Caption%20")` обрабатывается как `GetValue("Caption ")`. Не используйте символы "?" или "#" в идентификаторах ресурсов, так как они завершают оценку пути к ресурсу. Например, MyResource?3 трактуется как MyResource.

**ResourceManager** не только поддерживает доступ к строковым ресурсам приложения, но и дает возможность перечислять и проверять различные файловые ресурсы. Чтобы избежать конфликтов между файлами и другими ресурсами, которые создаются внутри файла, все индексированные пути к файлам располагаются в зарезервированном поддереве Files в **ResourceMap**. Например, файл `\Images\logo.png` соответствует имени ресурса `Files/images/logo.png`.

API [**StorageFile**](/uwp/api/Windows.Storage.StorageFile?branch=live) прозрачно обрабатывают ссылки на файлы как ресурсы и подходят для обычных сценариев использования. **ResourceManager** должен использоваться только для более сложных сценариев, например, если требуется переопределить текущий контекст.

### <a name="resourcecontext"></a>ResourceContext
Ресурсы-кандидаты выбираются по конкретному [**ResourceContext**](/uwp/api/Windows.ApplicationModel.Resources.Core.ResourceContext?branch=live) — набору значений квалификаторов ресурсов (языка, масштаба, контрастности и т. п.). Контекст по умолчанию применяет текущую конфигурацию приложения к каждому значению квалификатора, если только он не будет переопределен. Например, такие ресурсы, как изображения, могут иметь квалификатор масштаба, который зависит от монитора и, следовательно, варьируется от одного представления приложения к другому. Поэтому каждое представление приложения имеет отдельный контекст по умолчанию. Контекст по умолчанию для данного представления можно получить при помощи [**ResourceContext.GetForCurrentView**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.GetForCurrentView). При получении ресурса-кандидата нужно передать экземпляр **ResourceContext**, чтобы получить наиболее подходящее значение для данного представления.

## <a name="important-apis"></a>Важные API
* [ResourceLoader](/uwp/api/windows.applicationmodel.resources.resourceloader?branch=live)
* [ResourceManager](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live)
* [ResourceContext](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live)

## <a name="related-topics"></a>Статьи по теме
* [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](localize-strings-ui-manifest.md)
* [Загрузка изображений и ресурсов, адаптированных по масштабированию, теме, высокой контрастности и другим аспектам](images-tailored-for-scale-theme-contrast.md)

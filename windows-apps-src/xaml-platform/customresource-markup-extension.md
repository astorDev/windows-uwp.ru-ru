---
description: Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на ресурс, которая берется из пользовательской реализации поиска ресурсов. Поиск ресурсов выполняется реализацией класса CustomXamlResourceLoader.
title: Расширение разметки CustomResource
ms.assetid: 3A59A8DE-E805-4F04-B9D9-A91E053F3642
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d59a8662d430c527aa2f83aea945ee5bcf48642e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366436"
---
# <a name="customresource-markup-extension"></a>Расширение разметки {CustomResource}


Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на ресурс, которая берется из пользовательской реализации поиска ресурсов. Поиск ресурсов выполняется посредством реализации класса [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader).

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{CustomResource key}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| ключ | Ключ для запрашиваемого ресурса. Способ первоначального назначения ключа определяется в зависимости от реализации класса [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader), который в данный момент зарегистрирован для использования. |

## <a name="remarks"></a>Примечания

**CustomResource** — это способ получения значений, которые определены где-либо в пользовательском репозитории ресурсов. Этот метод является относительно сложным, и он не используется в большинстве сценариев приложений среды выполнения Windows.

Разрешение **CustomResource** в словаре ресурсов в данном разделе не описывается, так как может происходить совершенно по-разному в зависимости от реализации класса [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader).

Анализатор XAML среды выполнения Windows вызывает метод [**GetResource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resources.customxamlresourceloader.getresource) реализации класса [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader) при каждом использовании `{CustomResource}` в разметке. Параметр *resourceId*, передаваемый методу **GetResource**, берется из аргумента *key*; источником остальных входных параметров, а также свойств, к которым применяется это использование, является контекст.

Использование `{CustomResource}` не работает по умолчанию (базовая реализация [**GetResource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resources.customxamlresourceloader.getresource) не закончена). Чтобы создать допустимую ссылку на `{CustomResource}`, сделайте следующее:

1.  Создайте производный пользовательский класс от [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader) и переопределите метод [**GetResource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resources.customxamlresourceloader.getresource). Не вызывайте базовый класс в реализации.
2.  Задайте свойство [**CustomXamlResourceLoader.Current**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resources.customxamlresourceloader.current) для ссылки на класс в логике инициализации. Это необходимо сделать до загрузки XAML-кода уровня страницы, который включает использование расширения `{CustomResource}`. Например, **CustomXamlResourceLoader.Current** можно задать в конструкторе подкласса [**Application**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application), который создается в шаблонах кода программной части App.xaml.
3.  Теперь вы можете использовать расширения `{CustomResource}` в коде XAML, который приложение загружает как страницы, либо в словарях ресурсов XAML.

**CustomResource** — это расширение разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Все расширения разметки в XAML используют "\{«и»\}" символов в синтаксисе их атрибутов, который является соглашением, по которому обработчик XAML узнает, что расширение разметки должно обработать атрибут.

## <a name="related-topics"></a>См. также

* [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references)
* [**CustomXamlResourceLoader**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Resources.CustomXamlResourceLoader)
* [**GetResource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resources.customxamlresourceloader.getresource)


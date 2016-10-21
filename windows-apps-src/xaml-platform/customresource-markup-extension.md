---
author: jwmsft
description: "Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на ресурс, которая берется из пользовательской реализации поиска ресурсов. Поиск ресурсов выполняется реализацией класса CustomXamlResourceLoader."
title: "Расширение разметки CustomResource"
ms.assetid: 3A59A8DE-E805-4F04-B9D9-A91E053F3642
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: a0a4edc8d59219aea3a47a18ea991e4267782026

---

# Расширение разметки {CustomResource}

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Предоставляет значение для любого атрибута языка XAML с помощью анализа ссылки на ресурс, которая берется из пользовательской реализации поиска ресурсов. Поиск ресурсов выполняется посредством реализации класса [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327).

## Использование атрибутов XAML

``` syntax
<object property="{CustomResource key}" .../>
```

## Значения XAML

| Термин | Описание |
|------|-------------|
| key | Ключ для запрашиваемого ресурса. Способ первоначального назначения ключа определяется в зависимости от реализации класса [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327), который в данный момент зарегистрирован для использования. |

## Примечания.

**CustomResource** — это способ получения значений, которые определены где-либо в пользовательском репозитории ресурсов. Этот метод является относительно сложным, и он не используется в большинстве сценариев приложений среды выполнения Windows.

Разрешение **CustomResource** в словаре ресурсов в данном разделе не описывается, так как может происходить совершенно по-разному в зависимости от реализации класса [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327).

Анализатор XAML среды выполнения Windows вызывает метод [**GetResource**](https://msdn.microsoft.com/library/windows/apps/br243340) реализации класса [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327) при каждом использовании `{CustomResource}` в разметке. Параметр *resourceId*, передаваемый методу **GetResource**, берется из аргумента *key*; источником остальных входных параметров, а также свойств, к которым применяется это использование, является контекст.

Использование `{CustomResource}` не работает по умолчанию (базовая реализация [**GetResource**](https://msdn.microsoft.com/library/windows/apps/br243340) не закончена). Чтобы создать допустимую ссылку на `{CustomResource}`, сделайте следующее:

1.  Создайте производный пользовательский класс от [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327) и переопределите метод [**GetResource**](https://msdn.microsoft.com/library/windows/apps/br243340). Не вызывайте базовый класс в реализации.
2.  Задайте свойство [**CustomXamlResourceLoader.Current**](https://msdn.microsoft.com/library/windows/apps/br243328) для ссылки на класс в логике инициализации. Это необходимо сделать до загрузки XAML-кода уровня страницы, который включает использование расширения `{CustomResource}`. Например, **CustomXamlResourceLoader.Current** можно задать в конструкторе подкласса [**Application**](https://msdn.microsoft.com/library/windows/apps/br242324), который создается в шаблонах кода программной части App.xaml.
3.  Теперь вы можете использовать расширения `{CustomResource}` в коде XAML, который приложение загружает как страницы, либо в словарях ресурсов XAML.

**CustomResource**— это расширение разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы «\{» и «\}». Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

## Ссылки по теме

* [Ссылки на ресурсы ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273)
* [**CustomXamlResourceLoader**](https://msdn.microsoft.com/library/windows/apps/br243327)
* [**GetResource**](https://msdn.microsoft.com/library/windows/apps/br243340)




<!--HONumber=Aug16_HO3-->



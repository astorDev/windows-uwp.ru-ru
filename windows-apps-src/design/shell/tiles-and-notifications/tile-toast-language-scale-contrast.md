---
Description: Вы можете загружать в ваши плитки и всплывающие уведомления строки и изображения, соответствующие языку интерфейса, коэффициенту экранного увеличения, режиму контрастности и другим факторам времени исполнения.
title: Поддержка различных языков, масштабирования и высокой контрастности в плитках и всплывающих уведомлениях
template: detail.hbs
ms.date: 10/12/2017
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: aa6e93196d30c15374129eee7714604cfab7b82e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57601479"
---
# <a name="tile-and-toast-notification-support-for-language-scale-and-high-contrast"></a>Поддержка различных языков, масштабирования и высокой контрастности в плитках и всплывающих уведомлениях

Плитки и всплывающие уведомления могут загружать строки и изображения, адаптированные по контексту языка, [коэффициента масштабирования дисплея](../../layout/screen-sizes-and-breakpoints-for-responsive-design.md), темы, высокой контрастности и другим аспектам. Дополнительные сведения об использовании квалификаторы в именах файлов ресурсов см. в разделе [адаптировать ресурсы для языка, масштаба и квалификаторов](../../../app-resources/tailor-resources-lang-scale-contrast.md) и [значков приложений и логотипы](/windows/uwp/design/style/app-icons-and-logos).

Дополнительные сведения о преимуществах локализации приложений см. в разделе [Глобализация и локализация](../../globalizing/globalizing-portal.md).

## <a name="refer-to-a-string-resource-from-a-template"></a>Ссылка на строковый ресурс из шаблона

В шаблоне плитки или всплывающего уведомления можно ссылаться на строковый ресурс с помощью схемы URI (универсального кода ресурса) `ms-resource`, за которой следует идентификатор простого строкового ресурса. Например, если у вас есть файл Resources.resx, который содержит запись ресурса с именем Farewell, значит, у вас есть строковый ресурс с идентификатором Farewell. Дополнительные сведения об использовании идентификаторов строковых ресурсов и файлов ресурсов (.resw) см. в разделе [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../../app-resources/localize-strings-ui-manifest.md).

Вот как будет выглядеть ссылка на идентификатор строкового ресурса Farewell в разделе [text](/uwp/schemas/tiles/tilesschema/element-text?branch=live) шаблона с использованием `ms-resource`.

```xml
<text id="1">ms-resource:Farewell</text>
```

Если схема URI `ms-resource` не указывается, раздел text будет просто строковым литералом, а *не* ссылкой на идентификатор.

```xml
<text id="1">Farewell</text>
```

## <a name="refer-to-an-image-resource-from-a-template"></a>Ссылка на ресурс изображения из шаблона

В шаблоне плитки или всплывающего уведомления можно ссылаться на ресурс изображения с помощью схемы URI (универсального кода ресурса) `ms-appx`, за которой следует имя ресурса изображения. Это аналогично тому, как используются ссылки на ресурс изображения в разметке XAML (дополнительные сведения см. в разделе [Ссылки на изображения и другие ресурсы из кода и разметки XAML](../../../app-resources/images-tailored-for-scale-theme-contrast.md#reference-an-image-or-other-asset-from-xaml-markup-and-code)).

Например, папки можно назвать следующим образом.

```
\Assets\Images\contrast-standard\welcome.png
\Assets\Images\contrast-high\welcome.png
```

В этом случае имеется один ресурс изображения, а его имя (как абсолютный путь) — `/Assets/Images/welcome.png`. Вот как использовать это имя в шаблоне.

```xml
<image id="1" src="ms-appx:///Assets/Images/welcome.png"/>
```

Обратите внимание, как в этом примере URI за схемой `ms-appx` следует `://`, а далее указан абсолютный путь (начинается с `/`).

## <a name="hosting-and-loading-images-in-the-cloud"></a>Размещение и загрузка изображений в облаке

Схемы URI `ms-resource` и `ms-appx` выполняют автоматическое сопоставление квалификаторов для поиска ресурса, который больше всего подходит текущему контексту. Веб-схемы URI (например, `http`, `https` и `ftp`) не выполняют никакого автоматического сопоставления.

Вместо этого добавьте к URI вашего изображения строку запроса, описывающую запрошенное значение или значения квалификатора.

```xml
<image id="1" src="http://www.contoso.com/Assets/Images/welcome.png?ms-lang=en-US"/>
```

Затем в службе приложения, которая предоставляет изображения, реализуйте обработчик HTTP, который будет проверять и использовать строку запроса, чтобы определить, какое изображение требуется вернуть.

Необходимо также задать атрибуту [**addImageQuery**](/uwp/schemas/tiles/tilesschema/element-visual?branch=live) значение `true` в полезных данных XML-уведомления [плитки](/uwp/schemas/tiles/tilesschema/schema-root?branch=live) или [всплывающего уведомления](/uwp/schemas/tiles/toastschema/schema-root?branch=live). Атрибут **addImageQuery** отображается в элементах `visual`, `binding` и `image` плиток и всплывающих уведомлений. Явно указав **addImageQuery** для элемента, вы переопределите любое значение, заданное предку этого элемента. Например, значение `true` для атрибута **addImageQuery** в элементе `image` переопределяет значение `false` для атрибута **addImageQuery** в родительском элементе `binding`.

Это строки запросов, которые можно использовать.

| Квалификатор | Строка запроса | Пример |
| --------- | ------------ | ------- |
| Масштаб | ms-scale | ?ms-scale=400 |
| Язык | ms-lang | ?ms-lang=en-US |
| Контрастность | ms-contrast | ?ms-contrast=high |

Справочную таблицу всех возможных значений квалификаторов, которые можно использовать в строках запросов, см. в разделе [ResourceContext.QualifierValues](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues).

## <a name="important-apis"></a>Важные API

* [ResourceContext.QualifierValues](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues)

## <a name="related-topics"></a>Статьи по теме

* [Размеры экрана и точек останова для адаптивный Дизайн](../../layout/screen-sizes-and-breakpoints-for-responsive-design.md)
* [Настроить ресурсы для языка, масштаба и квалификаторов](../../../app-resources/tailor-resources-lang-scale-contrast.md)
* [Руководство по работе с ресурсами плиток и значков](app-assets.md).
* [Глобализация и локализация](../../globalizing/globalizing-portal.md)
* [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../../app-resources/localize-strings-ui-manifest.md)
* [Ссылаться на изображения и другие ресурсы из XAML-разметки и кода](../../../app-resources/images-tailored-for-scale-theme-contrast.md)
* [addImageQuery](/uwp/schemas/tiles/tilesschema/element-visual?branch=live)
* [Плитка схемы](/uwp/schemas/tiles/tilesschema/schema-root?branch=live)
* [Схема всплывающее уведомление](/uwp/schemas/tiles/toastschema/schema-root?branch=live)

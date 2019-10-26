---
Description: Используйте кодировку UTF-8 для обеспечения оптимальной совместимости между веб-приложениями и другими * nix платформами (UNIX, Linux и разновидностями), свести к сведению ошибки локализации и снизить затраты на тестирование.
title: Использование кодовой страницы Windows UTF-8
template: detail.hbs
ms.date: 06/12/2019
ms.topic: article
keywords: windows 10, uwp, глобализация, локализуемость, локализация
ms.localizationpriority: medium
ms.openlocfilehash: be3aade0289911f878d960fb62bde49b8ef840a8
ms.sourcegitcommit: 3a06cf3f8bd00e5e6eac3b38ee7e3c7cf4bc5197
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888739"
---
# <a name="use-the-utf-8-code-page"></a>Использование кодовой страницы UTF-8

Используйте кодировку [UTF-8](http://www.utf-8.com/) для обеспечения оптимальной совместимости между веб-приложениями и другими * nix платформами (UNIX, Linux и разновидностями), свести к сведению ошибки локализации и снизить затраты на тестирование.

UTF-8 — это универсальная кодовая страница для международного использования и поддерживает все кодовые точки Юникода с использованием кодировки переменной ширины в 1-6 байт. Он используется в Интернете по умолчанию, а также на платформах на основе NIX.

## <a name="-a-vs--w-apis"></a>— API-интерфейсы и-W
  
Интерфейсы API Win32 часто поддерживают варианты "-A" и "W".

— Варианты, распознаваемые кодовую страницу ANSI, настроенную в системе, и поддерживают `char*`, а варианты-W работают в кодировке UTF-16 и поддерживают `WCHAR`.

До недавнего времени Windows выделена «Юникод»-W Variant для API. Однако последние выпуски использовали кодовую страницу ANSI и — API-интерфейсы в качестве средства для поддержки приложений в кодировке UTF-8. Если кодовая страница ANSI настроена для UTF-8, то API-интерфейсы работают в кодировке UTF-8. Эта модель имеет преимущество поддержки существующего кода, построенного с помощью API-интерфейсов, без изменения кода.

## <a name="set-a-process-code-page-to-utf-8"></a>Задание кодовой страницы процесса UTF-8

Начиная с версии Windows 1903 (Май 2019), можно использовать свойство Активекодепаже в appxmanifest для упакованных приложений или манифест Fusion для неупакованных приложений, чтобы заставить процесс использовать UTF-8 в качестве кодовой страницы процесса.

Вы можете объявить это свойство и целевой объект или выполнить его в более ранних сборках Windows, но необходимо как обычно выполнять обнаружение и преобразование кодовых страниц прежних версий. При использовании минимальной целевой версии Windows версии 1903 кодовая страница процесса всегда будет UTF-8, поэтому можно избежать обнаружения и преобразования кодовой страницы прежних версий.

## <a name="examples"></a>Примеры.

**Манифест appx для упакованного приложения:**

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Package xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
         ...
         xmlns:uap7="http://schemas.microsoft.com/appx/manifest/uap/windows10/7"
         xmlns:uap8="http://schemas.microsoft.com/appx/manifest/uap/windows10/8"
         ...
         IgnorableNamespaces="... uap7 uap8 ...">

  <Applications>
    <Application ...>
      <uap7:Properties>
        <uap8:ActiveCodePage>UTF-8</uap8:ActiveCodePage>
      </uap7:Properties>
    </Application>
  </Applications>
</Package>
```

**Манифест Fusion для неупакованного приложения Win32:**

``` xaml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1">
  <assemblyIdentity type="win32" name="..." version="6.0.0.0"/>
  <application>
    <windowsSettings>
      <activeCodePage xmlns="http://schemas.microsoft.com/SMI/2019/WindowsSettings">UTF-8</activeCodePage>
    </windowsSettings>
  </application>
</assembly>
```

> [!NOTE]
> Добавление манифеста в существующий исполняемый файл из командной строки с `mt.exe -manifest <MANIFEST> -outputresource:<EXE>;#1`

## <a name="code-page-conversion"></a>Преобразование кодовой страницы

Поскольку Windows изначально работает в кодировке UTF-16 (`WCHAR`), может потребоваться преобразовать данные UTF-8 в UTF-16 (или наоборот) для взаимодействия с API Windows.

[MultiByteToWideChar](https://docs.microsoft.com/windows/desktop/api/stringapiset/nf-stringapiset-multibytetowidechar) и [WideCharToMultiByte](https://docs.microsoft.com/windows/desktop/api/stringapiset/nf-stringapiset-widechartomultibyte) позволяют выполнить преобразование между UTF-8 и utf-16 (`WCHAR`) (и другими кодовыми страницами). Это особенно полезно, когда устаревший API Win32 может понимать только `WCHAR`. Эти функции позволяют преобразовать входные данные UTF-8 в `WCHAR` для передачи в API-интерфейс, а затем преобразовать все результаты при необходимости.
При использовании этих функций с `CodePage`, для которого задано значение `CP_UTF8`, используйте `dwFlags` либо `0`, либо `MB_ERR_INVALID_CHARS`, иначе происходит `ERROR_INVALID_FLAGS`.

Примечание. `CP_ACP` равно `CP_UTF8` только в том случае, если работает в Windows версии 1903 (Май 2019) или выше, а описанное выше свойство Активекодепаже имеет значение UTF-8. В противном случае она учитывает устаревшую системную кодовую страницу. Рекомендуется явно использовать `CP_UTF8`.

## <a name="related-topics"></a>Статьи по теме

- [Кодовые страницы](https://docs.microsoft.com/windows/desktop/Intl/code-pages)
- [Идентификаторы кодовых страниц](https://docs.microsoft.com/windows/desktop/Intl/code-page-identifiers)

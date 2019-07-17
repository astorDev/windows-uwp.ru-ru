---
Description: Используйте символ UTF-8 кодирования для оптимальной совместимости между веб-приложений и других * свести к минимуму ошибки локализации платформы на основе nix (Unix, Linux и вариантов) и снизить издержки тестирования.
title: Используют кодовую страницу Windows UTF-8
template: detail.hbs
ms.date: 06/12/2019
ms.topic: article
keywords: windows 10, uwp, глобализация, локализуемость, локализация
ms.localizationpriority: medium
ms.openlocfilehash: a9386b31d16796c68d41a27ab48a5b2c9a9a342b
ms.sourcegitcommit: 734aa941dc675157c07bdeba5059cb76a5626b39
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68141804"
---
# <a name="use-the-utf-8-code-page"></a>Использование кодовой страницы UTF-8

Используйте [UTF-8](http://www.utf-8.com/) кодировка для оптимальной совместимости между веб-приложений и других символов * свести к минимуму ошибки локализации платформы на основе nix (Unix, Linux и вариантов) и снизить издержки тестирования.

UTF-8 является универсальной кодовую страницу для интернационализации и поддерживает все кодовые позиции Юникода с использованием кодировки переменным 1 – 4 байта. Он используется поддержки в Интернете и значение по умолчанию для * nix-платформах.

## <a name="-a-vs--w-apis"></a>-A и -W API-интерфейсов
  
API-интерфейсов Win32 часто поддержки и - и -W вариантов.

-A варианты распознает кодовой страницы ANSI, настроенной в системе и поддержка `char*`, тогда как -W варианты работы в UTF-16 и поддержки `WCHAR`.

До недавнего времени Windows выделяются «Unicode» -W вариантов относительно - интерфейсов API. Тем не менее последние выпуски использовали кодовую страницу ANSI и - API, что означает, что для ознакомления UTF-8 поддерживает к приложениям. Если кодовая страница ANSI для UTF-8, API-интерфейсы работают в UTF-8. Эта модель имеет преимущество поддержки существующего кода, созданного с помощью - интерфейсов API без изменения кода.

## <a name="set-a-process-code-page-to-utf-8"></a>Значение процесса кодовую страницу UTF-8

Начиная с версии Windows 1903 (может обновлять 2019 г.) свойство ActiveCodePage в манифест appxmanifest упакованные приложения или манифесте fusion для работе приложений, чтобы принудительно запустить процесс для использования в качестве процесса кодовую страницу UTF-8.

Это свойство можно объявить и создает целевой объект и запуска на более ранних Windows, но необходимо обрабатывать Обнаружение устаревшего кода страницы и преобразования обычным образом. Минимальное целевое 1903 версии Windows до версии кодовая страница процесса всегда будет UTF-8, поэтому Обнаружение устаревшего кода страницы и преобразования можно избежать.

## <a name="examples"></a>Примеры

**Манифест AppX для упакованного приложения:**

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

**Fusion манифест для приложения распакован Win32:**

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
> Добавление манифеста для имеющегося исполняемого файла из командной строки с помощью `mt.exe -manifest <MANIFEST> -outputresource:<EXE>;#1`

## <a name="code-page-conversion"></a>Преобразования кодовых страниц

Как Windows работает в собственном коде в кодировке UTF-16 (`WCHAR`), может потребоваться преобразовать данные UTF-8 в UTF-16 (или наоборот) для взаимодействия с API-интерфейсов Windows.

[MultiByteToWideChar](https://docs.microsoft.com/windows/desktop/api/stringapiset/nf-stringapiset-multibytetowidechar) и [WideCharToMultiByte](https://docs.microsoft.com/windows/desktop/api/stringapiset/nf-stringapiset-widechartomultibyte) позволить взаимных UTF-8 и UTF-16 (`WCHAR`) (и другие кодовые страницы). Это особенно полезно, когда устаревшего API-интерфейса Win32 может только предполагать `WCHAR`. Эти функции позволяют преобразовать входные данные UTF-8 в `WCHAR` передать в -W API и преобразовать любые результаты обратно в том случае, при необходимости.
При использовании этих функций с `CodePage` присвоено `CP_UTF8`, использовать `dwFlags` либо `0` или `MB_ERR_INVALID_CHARS`, в противном случае `ERROR_INVALID_FLAGS` происходит.

Примечание: `CP_ACP` соответствует `CP_UTF8` только в том случае, если задано значение UTF-8 под управлением Windows версии 1903 (может обновлять 2019 г.) или более поздней версии и ActiveCodePage свойство, описанное выше. В противном случае он учитывает устаревших системную кодовую страницу. Мы рекомендуем использовать `CP_UTF8` явным образом.

## <a name="related-topics"></a>См. также

- [Кодовые страницы](https://docs.microsoft.com/windows/desktop/Intl/code-pages)
- [Идентификаторы кодовой страницы](https://docs.microsoft.com/windows/desktop/Intl/code-page-identifiers)

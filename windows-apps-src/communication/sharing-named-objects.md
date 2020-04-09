---
title: Совместное использование именованных объектов
description: В этом разделе объясняется, как использовать именованные объекты между приложениями универсальная платформа Windows (UWP) и приложениями Win32.
ms.date: 04/06/2020
ms.topic: article
keywords: windows 10, uwp
ms.openlocfilehash: 95bbecd85b1dfa6f6e12766c082f3338de549677
ms.sourcegitcommit: 2d375e1c34473158134475af401532cc55fc50f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80888576"
---
# <a name="sharing-named-objects"></a>Совместное использование именованных объектов

В этом разделе объясняется, как использовать именованные объекты между приложениями универсальная платформа Windows (UWP) и приложениями Win32.

## <a name="named-objects-in-packaged-applications"></a>Именованные объекты в упакованных приложениях

[Именованные объекты](/windows/win32/sync/object-names) предоставляют процессам простой способ совместного использования дескрипторов объектов. После того как процесс создал именованный объект, другие процессы могут использовать имя для вызова соответствующей функции для открытия маркера объекта. Именованные объекты обычно используются для [синхронизации потоков](/windows/win32/sync/interprocess-synchronization) и [взаимодействия между процессами](/windows/uwp/communication/interprocess-communication).

По умолчанию Упакованные приложения могут обращаться только к именованным объектам, которые они создали. Для совместного использования именованных объектов с упакованными приложениями разрешения должны быть заданы при создании объектов, а имена должны уточняться при открытии объектов.

## <a name="creating-named-objects"></a>Создание именованных объектов

Именованные объекты создаются с помощью соответствующего `Create` API:

* [CreateEvent](/windows/win32/api/synchapi/nf-synchapi-createeventexw)
* [CreateFileMapping](/windows/win32/api/memoryapi/nf-memoryapi-createfilemappingw)
* [CreateMutex](/windows/win32/api/synchapi/nf-synchapi-createmutexexw)
* [Createsemaphore-](/windows/win32/api/synchapi/nf-synchapi-createsemaphoreexw)
* [Сбой createwaitabletimer](/windows/win32/api/synchapi/nf-synchapi-createwaitabletimerexw)

Все эти API совместно используют параметр `LPSECURITY_ATTRIBUTES`, который позволяет вызывающей стороне указать [списки управления доступом (ACL)](/previous-versions/windows/desktop/legacy/aa379560(v=vs.85)) для управления тем, какие процессы могут получить доступ к объекту. Для совместного использования именованных объектов в упакованных приложениях необходимо предоставить разрешения в списках управления доступом при создании именованных объектов.

Идентификаторы безопасности (SID) представляют удостоверения в списках ACL. Каждое упакованное приложение имеет собственный идентификатор безопасности на основе его имени семейства пакетов. Идентификатор безопасности для упакованного приложения можно создать, передав его имя семейства пакетов в [деривеаппконтаинерсидфромаппконтаинернаме](/windows/win32/api/userenv/nf-userenv-deriveappcontainersidfromappcontainername).

> [!NOTE]
> Имя семейства пакетов можно найти с помощью редактора манифеста пакета в Visual Studio во время разработки с помощью [центра партнеров](/windows/uwp/publish/view-app-identity-details) для приложений, опубликованных с помощью Microsoft Store, или с помощью команды PowerShell [Get-AppxPackage](/powershell/module/appx/get-appxpackage?view=win10-ps) для уже установленных приложений.

[Этот образец](/windows/win32/api/securityappcontainer/nf-securityappcontainer-getappcontainernamedobjectpath#examples) демонстрирует базовый шаблон, необходимый для управления доступом к именованному объекту. Чтобы использовать именованные объекты в упакованных приложениях, создайте структуру [EXPLICIT_ACCESS](/windows/win32/api/accctrl/ns-accctrl-explicit_access_w) для каждого приложения:

* `grfAccessMode = GRANT_ACCESS`
* `grfAccessPermissions =` соответствующие разрешения на основе объекта и предполагаемого использования
    * [Универсальные права доступа](/windows/win32/secauthz/generic-access-rights)
    * [Безопасность объекта синхронизации и права доступа](/windows/win32/sync/synchronization-object-security-and-access-rights)
    * [Безопасность и права доступа для сопоставления файлов](/windows/win32/memory/file-mapping-security-and-access-rights)
* `grfInheritance = NO_INHERITANCE`
* `Trustee.TrusteeForm = TRUSTEE_IS_SID`
* `Trustee.TrusteeType = TRUSTEE_IS_USER`
* `Trustee.ptstrName =` идентификатор безопасности, полученный от [деривеаппконтаинерсидфромаппконтаинернаме](/windows/win32/api/userenv/nf-userenv-deriveappcontainersidfromappcontainername)

Заполняя параметр `LPSECURITY_ATTRIBUTES` в `Create` вызовы с правилами `EXPLICIT_ACCESS` для упакованных приложений, можно предоставить доступ к этим приложениям, чтобы открыть именованный объект.

> [!NOTE]
> Приложения Win32 могут обращаться ко всем именованным объектам, созданным упакованными приложениями, при условии, что они определяют имена объектов при [их открытии](#opening-named-objects). Им не нужно предоставлять доступ.

## <a name="opening-named-objects"></a>Открытие именованных объектов

Именованные объекты открываются путем передачи имени соответствующему `Open` API:

* [опеневент](/windows/win32/api/synchapi/nf-synchapi-openeventw)
* [OpenFileMapping](/windows/win32/api/memoryapi/nf-memoryapi-openfilemappingw)
* [опенмутекс](/windows/win32/api/synchapi/nf-synchapi-openmutexw)
* [опенсемафоре](/windows/win32/api/synchapi/nf-synchapi-opensemaphorew)
* [опенваитаблетимер](/windows/win32/api/synchapi/nf-synchapi-openwaitabletimerw)

Именованные объекты, созданные упакованным приложением, создаются в пространстве имен приложения, в противном случае известном как путь к именованному объекту. При открытии именованных объектов, созданных упакованным приложением, имена объектов должны иметь префикс с путем создания именованного объекта приложения.

[Жетаппконтаинернамедобжектпас](/windows/win32/api/securityappcontainer/nf-securityappcontainer-getappcontainernamedobjectpath) вернет путь к именованному объекту для упакованного приложения на основе его идентификатора безопасности. Идентификатор безопасности для упакованного приложения можно создать, передав его имя семейства пакетов в [деривеаппконтаинерсидфромаппконтаинернаме](/windows/win32/api/userenv/nf-userenv-deriveappcontainersidfromappcontainername).

> [!NOTE]
> Имя семейства пакетов можно найти с помощью редактора манифеста пакета в Visual Studio во время разработки с помощью [центра партнеров](/windows/uwp/publish/view-app-identity-details) для приложений, опубликованных с помощью Microsoft Store, или с помощью команды PowerShell [Get-AppxPackage](/powershell/module/appx/get-appxpackage?view=win10-ps) для уже установленных приложений.

При открытии именованных объектов, созданных упакованным приложением, используйте формат `<PATH>\<NAME>`:

* Замените `<PATH>` на путь к именованному объекту для приложения.
* Замените `<NAME>` именем объекта.

> [!NOTE]
> Префиксные имена объектов с `<PATH>` требуются только в том случае, если упакованное приложение создало объект. Именованные объекты, созданные приложениями Win32, не обязательно должны быть квалифицированы, хотя при [создании](#creating-named-objects)объектов доступ к ним по-прежнему должен быть предоставлен.

## <a name="remarks"></a>Примечания

Именованные объекты в упакованных приложениях изолированы по умолчанию для сохранения безопасности и обеспечивают поддержку событий жизненного цикла приложения, таких как приостановка и завершение. Совместное использование именованных объектов в приложениях обеспечивает строгие ограничения привязки и управления версиями, а также требует, чтобы каждое приложение было устойчивым к жизненному циклу других. По этим причинам рекомендуется совместно использовать именованные объекты только между приложениями одного издателя.

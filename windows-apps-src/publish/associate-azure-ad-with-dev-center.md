---
author: jnHs
Description: In order to add and manage account users, you must first associate your Dev Center account with your organization's Azure Active Directory.
title: Связывание Azure Active Directory с учетной записью Центра разработки
ms.author: wdg-dev-content
ms.date: 08/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, azure ad, клиент azure, клиент aad, клиент azure ad, управление клиентами, клиенты
ms.localizationpriority: medium
ms.openlocfilehash: dd729d76705849c981516109da39bbd27c140286
ms.sourcegitcommit: 753dfcd0f9fdfc963579dd0b217b445c4b110a18
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "2858034"
---
# <a name="associate-azure-active-directory-with-your-dev-center-account"></a>Связывание Azure Active Directory с учетной записью Центра разработки

Чтобы [добавлять пользователей учетной записи и управлять ими](add-users-groups-and-azure-ad-applications.md), сначала нужно связать учетную запись Центра разработки с Azure Active Directory вашей организации. 

Центр разработки для Windows использует Azure AD для управления доступом нескольких пользователей к учетной записи и управления ими. Если ваша организация уже использует Office 365 или другие бизнес-службы от Майкрософт, то у вас уже есть Azure AD. В противном случае можно создать новый клиент Azure AD из Центра разработки без дополнительной платы.

> [!TIP]
> В этом разделе описана программа для разработчиков приложений для Windows, однако связывание клиента и управление пользователями работает аналогично для учетных записей Программы классических приложений для Windows (см. дополнительные сведения в разделе [Программа классических приложений для Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#add-and-manage-account-users)) и Программы для разработчиков оборудования для Windows (где ссылки на роль **Менеджер** также применимы к аппаратным учетным записям с ролью **Администратор**; см. дополнительные сведения в разделе [Администрирование информационной панели](https://docs.microsoft.com/windows-hardware/drivers/dashboard/dashboard-administration)).

Один клиент Azure AD можно связать с несколькими учетными записями Центра разработки. Для добавления нескольких пользователей учетной записи достаточно иметь один клиент Azure AD, связанный с учетной записью Центра разработки, однако вы также можете добавить несколько клиентов Azure AD к одной учетной записи Центра разработки. Любой пользователь с ролью **Менеджер** в учетной записи Центра разработки будет иметь возможность добавления и удаления клиентов Azure AD.

> [!IMPORTANT]
> После связывания учетной записи Центра разработки с клиентом Azure AD для добавления пользователей и управления ими в этом клиенте потребуется выполнить вход в Центр разработки, используя учетную запись пользователя в том клиенте, который имеет роль **Менеджер**.


## <a name="associate-your-dev-center-account-with-your-organizations-existing-azure-ad-tenant"></a>Связывание учетной записи Центра разработки с существующим клиентом Azure AD организации

Если ваша организация уже использует Azure AD, выполните следующие действия, чтобы связать свою учетную запись Центра разработки.

1.  Из [панели мониторинга Центр разработчиков Windows](https://partner.microsoft.com/dashboard)выберите значок шестеренки (рядом с правом верхнем углу панели мониторинга) и выберите пункт **Параметры учетной записи**. В меню **Параметры** выберите **клиентов**.
2.  Выберите пункт **Привязка Azure AD к учетной записи Центра разработки**.
3.  Введите свои учетные данные Azure AD для клиента, который требуется привязать.
4.  Проверьте название организации и доменное имя клиента Azure Active Directory. Чтобы установить связь, нажмите кнопку **Подтвердить**.
5.  Если связь успешно установлена, вы сможете добавлять пользователей учетной записи и управлять ими в разделе **Пользователи** Центра разработки.

> [!IMPORTANT]
> Для создания новых пользователей или внесении других изменений в Azure AD потребуется выполнить вход в клиент Azure AD, используя учетную запись, имеющую [разрешения глобального администратора](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) для этого клиента. При этом разрешения глобального администратора не требуются для того, чтобы связать клиента или добавить пользователей, которые уже существуют в клиенте, к учетной записи Центра разработки.


## <a name="create-a-brand-new-azure-ad-to-associate-with-your-dev-center-account"></a>Создание новой службы Azure AD для связывания со своей учетной записью Центра разработки

Если необходимо настроить новую службу Azure AD, чтобы связать ее с учетной записью Центра разработки, выполните следующие действия.

1.  Из [панели мониторинга Центр разработчиков Windows](https://partner.microsoft.com/dashboard)выберите значок шестеренки (рядом с правом верхнем углу панели мониторинга) и выберите пункт **Параметры учетной записи**. В меню **Параметры** выберите **клиентов**.
2.  Нажмите кнопку **Создать новую службу Azure AD**.
3.  Введите сведения для внесения новой службы Azure AD в каталог:
    - **Доменное имя**: уникальное имя, которое будет использоваться для вашего домена Azure AD вместе с «.onmicrosoft.com». Например, если вы укажете здесь example, домен Azure AD будет иметь вид example.onmicrosoft.com.
    - **Контактный адрес электронной почты**: адрес электронной почты, по которому мы сможем при необходимости связаться с вами по вопросам, связанным с вашей учетной записью.
    - **Сведения о пользовательской учетной записи глобального администратора**: имя, фамилия, имя пользователя и пароль для новой учетной записи глобального администратора.
4.  Щелкните **Создать**, чтобы подтвердить новые сведения о домене и учетной записи.
5.  Выполните вход с именем пользователя и паролем глобального администратора Azure AD, чтобы начать [добавлять дополнительных пользователей учетных записей](add-users-groups-and-azure-ad-applications.md).


## <a name="manage-azure-ad-tenant-associations"></a>Управление сопоставлениями клиента Azure AD

После связывания клиента Azure AD с учетной записью Центра разработки можно добавлять новые клиенты или удалить существующие на странице **Клиенты**.


### <a name="add-multiple-azure-ad-tenants-to-your-dev-center-account"></a>Добавление нескольких клиентов AzureAD к учетной записи Центра разработки

Любой пользователь, имеющий роль **Менеджер** в учетной записи Центра разработки, может связывать клиентов Azure AD с учетной записью.

Чтобы связать новый клиент, выберите пункт **Связать новый клиент Azure AD**, а затем выполните указанные выше действия. Обратите внимание, что вам потребуется ввести учетные данные в клиенте Azure AD, который требуется связать.


### <a name="remove-an-azure-ad-tenant-from-your-dev-center-account"></a>Удаление клиента Azure AD из учетной записи Центра разработки

Любой пользователь, имеющий роль **Менеджер** в учетной записи Центра разработки, может удалять клиентов Azure AD из учетной записи.

> [!IMPORTANT]
> При удалении клиента все пользователи, которые были добавлены к учетной записи Центра разработки из этого клиента, потеряют возможность входа в эту учетную запись. 

Чтобы удалить клиента, найдите его имя на странице " **Клиенты** " (в **Параметры учетной записи**), а затем выберите **Удалить**. Вам будет предложено подтвердить удаление клиента. После этого ни один пользователь Центра разработки в этом клиенте не сможет войти в учетную запись Центра разработки и все разрешения, настроенные для этих пользователей, будут удалены.

> [!TIP]
> Невозможно удалить клиент, если вы вошли в Центр разработки с помощью учетной записи в этом клиенте. Чтобы удалить клиент, необходимо выполнить вход в Центр разработки в качестве **Менеджера** для другого клиента, который связан с данной учетной записью. При наличии только одного клиента, связанного с учетной записью, клиент можно удалить только после входа с помощью учетной записи Майкрософт, которая использовалась для открытия данной учетной записи.


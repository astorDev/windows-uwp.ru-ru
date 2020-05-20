---
title: Список пакетов NuGet для библиотеки пользовательского интерфейса Windows
description: Здесь перечислены пакеты NuGet для библиотеки пользовательского интерфейса Windows
ms.topic: article
ms.date: 04/15/2020
keywords: windows 10, uwp, пакет средств разработки
ms.openlocfilehash: 2bda405977733a6191c4434fd8bd2c63b2ce10ce
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580711"
---
# <a name="windows-ui-library-nuget-packages"></a>Пакеты NuGet для библиотеки пользовательского интерфейса Windows

NuGet — это стандартный встроенный в Visual Studio диспетчер пакетов для приложений .NET. В открытом решении выберите меню *Инструменты*, *Диспетчер пакетов NuGet*, *Управление пакетами NuGet для решения...* , чтобы открыть пользовательский интерфейс.  Введите имя одного из перечисленных ниже пакетов, чтобы найти его в Интернете.

| Имя пакета NuGet | Описание |
| --- | --- |
| [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml/) | Элементы управления для приложений UWP. Включает API из этих пространств имен: [Microsoft.UI.Xaml](/uwp/api/microsoft.ui.xaml), [Microsoft.UI.Xaml.Automation.Peers](/uwp/api/microsoft.ui.xaml.automation.peers), [Microsoft.Ui.Xaml.Controls](/uwp/api/microsoft.ui.xaml.controls), [Microsoft.UI.Xaml.Controls.Primitives](/uwp/api/microsoft.ui.xaml.controls.primitives), [Microsoft.UI.Xaml.CustomAttributes](/uwp/api/microsoft.ui.xaml.customattributes), [Microsoft.UI.Xaml.Media](/uwp/api/microsoft.ui.xaml.media), [Microsoft.Ui.Xaml.XamlTypeInfo](/uwp/api/microsoft.ui.xaml.xamltypeinfo) |
| [Microsoft.UI.Xaml.Core.Direct](https://www.nuget.org/packages/Microsoft.UI.Xaml.Core.Direct) | Позволяет применять API [XamlDirect](/uwp/api/microsoft.ui.xaml.core.direct.xamldirect) в более ранних версиях Windows 10, не требуя писать специальный код для работы с несколькими целевыми версиями Windows 10. |


## <a name="search-in-visual-studio"></a>Поиск в Visual Studio

При поиске в диспетчере пакетов Visual Studio вы увидите список, аналогичный этому (номера версий могут отличаться), но имена должны быть такими же.

![](images/NugetPackages.png)

## <a name="update-nuget-packages"></a>Обновление пакетов NuGet

Мы регулярно обновляем библиотеку пользовательского интерфейса Windows новыми элементами управления, службами, API и, прежде всего, исправлениями ошибок. Чтобы убедиться, что вы используете последнюю версию, откройте проект в Visual Studio, в меню **Инструменты** выберите **Диспетчер пакетов NuGet** -> **Управление пакетами NuGet для решения...** и выберите вкладку *Обновления*. Выберите пакет, который необходимо обновить, и нажмите кнопку установки, чтобы обновить его до последней версии.

## <a name="getting-started"></a>Начало работы

Дополнительные инструкции по использованию этих пакетов NuGet в собственных проектах см. в статье [Начало работы с библиотекой пользовательского интерфейса Windows](getting-started.md).

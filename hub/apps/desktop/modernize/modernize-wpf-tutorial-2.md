---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы XAML UWP, создавать MSIX-пакеты и включать другие актуальные компоненты в приложения WPF.
title: Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands
ms.topic: article
ms.date: 01/24/2020
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, объекты XAML Island
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 6bb90fb9cbe7c9f54f60fd1920f0e73e174a3772
ms.sourcegitcommit: df0cd9c82d1c0c17ccde424e3c4a6ff680c31a35
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80482583"
---
# <a name="part-2-add-a-uwp-inkcanvas-control-using-xaml-islands"></a>Часть 2. Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands

Это вторая часть учебника, в котором рассматривается модернизация примера классического приложения WPF с именем Contoso Expenses. Общие сведения об этом учебнике, предварительные требования и инструкции по скачиванию примера приложения см. в [учебнике по модернизации приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили инструкции из [части 1](modernize-wpf-tutorial-1.md).

В вымышленном сценарии для этого учебника группа разработки компании Contoso хочет добавить в приложение Contoso Expenses поддержку цифровых подписей. Элемент управления UWP **InkCanvas** будет является отличным выбором для этого сценария, так как он поддерживает функции цифрового рукописного ввода и искусственного интеллекта, например возможность распознавания текста и фигур. Его можно применить в формате заключенного в оболочку элемента управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas), который предоставляется в наборе средств сообщества Windows. Этот элемент управления является оболочкой для интерфейса и функциональных возможностей элемента управления UWP **InkCanvas**, позволяя использовать его в приложении WPF. Подробные сведения о заключенных в оболочку элементах управления UWP см. в статье [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md).

## <a name="configure-the-project-to-use-xaml-islands"></a>Настройка проекта для использования XAML Islands

Прежде чем добавлять элемент управления **InkCanvas** в приложение Contoso Expenses, необходимо настроить в проекте поддержку XAML Islands для UWP.

1. В Visual Studio 2019 щелкните правой кнопкой мыши проект **ContosoExpenses.Core** в **обозревателе решений** и выберите **Управление пакетами NuGet**.

    ![Меню "Управление пакетами NuGet" в Visual Studio](images/wpf-modernize-tutorial//ManageNuGetPackages.png)

2. В окне **диспетчера пакетов NuGet** щелкните **Просмотр**. Найдите пакет `Microsoft.Toolkit.Wpf.UI.Controls` и установите версию 6.0.0 или более позднюю.

    > [!NOTE]
    > Этот пакет содержит всю необходимую инфраструктуру для размещения XAML Islands для UWP в приложении WPF, включая заключенный в оболочку элемент управления UWP **InkCanvas**. Аналогичный пакет с именем `Microsoft.Toolkit.Forms.UI.Controls` доступен для приложений Windows Forms.

3. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** в **обозревателе решений** и выберите команду **Добавить > Новый проект**.

4. Выберите **файл манифеста приложения**, присвойте ему имя **app.manifest**и нажмите **Добавить**. Подробные сведения о манифестах приложения см. в [этой статье](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests).

5. В файле манифеста раскомментируйте следующий элемент `<supportedOS>` для Windows 10.

    ```xml
    <!-- Windows 10 -->
    <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
    ```

6. В файле манифеста найдите следующий закомментированный элемент `<application>`.

    ```xml
    <!--
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
        <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true</dpiAware>
      </windowsSettings>
    </application>
    -->
    ```

7. Удалите этот раздел и замените его приведенным ниже XML-кодом. Этот код включает в приложении поддержку DPI и улучшает обработку некоторых факторов масштабирования, поддерживаемых в Windows 10.

    ```xml
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
          <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/PM</dpiAware>
          <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2, PerMonitor</dpiAwareness>
      </windowsSettings>
    </application>
    ```

8. Сохраните и закройте файл `app.manifest`.

9. В **обозревателе решений** щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Свойства**.

10. В разделе **Ресурсы** на вкладке **Приложение** убедитесь, что в раскрывающемся списке **Манифест** выбрано значение **app.manifest**.

    ![Манифест приложения .NET Core](images/wpf-modernize-tutorial/NetCoreAppManifest.png)

11. Сохраните изменения в свойствах проекта.

## <a name="add-an-inkcanvas-control-to-the-app"></a>Добавление элемента управления InkCanvas в приложение

Теперь, когда вы настроили в проекте XAML Islands для UWP, вы можете добавить в приложение заключенный в оболочку элемент управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas).

1. В **обозревателе решений** разверните папку **Представления** для проекта **ContosoExpenses.Core** и дважды щелкните файл **ExpenseDetail.xaml**.

2. В элементе **Окно** в верхней части XAML-файла добавьте следующий атрибут. Он указывает на пространство имен XAML, в котором находится заключенный в оболочку элемент управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas).

    ```xml
    xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    Когда вы добавите этот атрибут, элемент **Окно** будет выглядеть следующим образом.

    ```xml
    <Window x:Class="ContosoExpenses.Views.ExpenseDetail"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            xmlns:converters="clr-namespace:ContosoExpenses.Converters"
            DataContext="{Binding Source={StaticResource ViewModelLocator}, Path=ExpensesDetailViewModel}"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Expense Detail" Height="500" Width="800"
            Background="{StaticResource HorizontalBackground}">
    ```

4. В файле **ExpenseDetail.xaml** найдите закрывающий тег `</Grid>` прямо перед комментарием `<!-- Chart -->`. Добавьте следующий XAML-код непосредственно перед этим закрывающим тегом `</Grid>`. Этот код XAML добавляет элемент управления **InkCanvas** (с префиксом **toolkit**, который мы ранее определили как пространство имен), и простой тег **TextBlock** в качестве заголовка для нашего элемента управления.

    ```xml
    <TextBlock Text="Signature:" FontSize="16" FontWeight="Bold" Grid.Row="5" />

    <toolkit:InkCanvas x:Name="Signature" Grid.Row="6" />
    ```

5. Сохраните файл **ExpenseDetail.xaml**.

6. Нажмите клавишу F5, чтобы открыть приложение в отладчике.

7. Выберите сотрудника из списка, а затем выберите одну из статей расходов. Обратите внимание, что страница сведений о статье расходов содержит место для элемента управления **InkCanvas**.

    ![Элемент Ink Canvas только для ввода пером](images/wpf-modernize-tutorial/InkCanvasPenOnly.png)

    Если у вас есть устройство, которое поддерживает цифровое перо, например Surface, и вы выполняете инструкции этого учебника на физическом компьютере, попробуйте использовать это устройство. Вы увидите, что на экране появится цифровая надпись. Если же у вас нет устройства, поддерживающего ввод пером, попытка расписаться с помощью мыши не даст результата. Это связано с тем, что элемент управления **InkCanvas** по умолчанию включен только для цифровых перьев. Однако мы можем изменить это поведение.

8. Выберите приложение и дважды щелкните файл **ExpenseDetail.xaml.cs** в папке **Views** для проекта **ContosoExpenses.Core**.

9. В верхней части класса добавьте следующее объявление пространства имен.

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

10. Найдите конструктор `ExpenseDetail()`.

11. Добавьте следующую строку кода после метода `InitializeComponent()` и сохраните файл кода.

    ```csharp
    Signature.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    Вы можете настроить интерфейс рукописного ввода с помощью объекта **InkPresenter**. Этот код использует свойство **InputDeviceTypes** для поддержки мыши в качестве устройства рукописного ввода.

12. Снова нажмите клавишу F5, чтобы повторно скомпилировать приложение и открыть его в отладчике. Выберите сотрудника из списка, а затем выберите одну из статей расходов.

13. Попробуйте нарисовать что-нибудь с помощью мыши в области для подписи. Вы увидите, что теперь на экране появляется цифровая надпись.

    ![Подпись](images/wpf-modernize-tutorial/Signature.png)

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе работы с учебником вы уже добавили элемент управления UWP **InkCanvas** в приложение Contoso Expenses. Теперь вы готовы перейти к статье [Часть 3. Добавление элемента управления CalendarView универсальной платформы Windows с помощью XAML Islands](modernize-wpf-tutorial-3.md).

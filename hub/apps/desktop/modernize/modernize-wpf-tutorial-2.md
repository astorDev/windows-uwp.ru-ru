---
description: Этот учебник демонстрирует добавление пользовательских интерфейсах XAML UWP, создание MSIX пакеты и другие современные компоненты включить в приложении WPF.
title: Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, о-ва xaml
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 35b6886389640c7960c4120772c169161779ab68
ms.sourcegitcommit: 734aa941dc675157c07bdeba5059cb76a5626b39
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68141841"
---
# <a name="part-2-add-a-uwp-inkcanvas-control-using-xaml-islands"></a>Часть 2. Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands

Это вторая часть учебник, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Обзор учебника, предварительные требования и инструкции для загрузки примера приложения, см. в разделе [руководства: Модернизация приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили [часть 1](modernize-wpf-tutorial-1.md).

В вымышленной сценарии работы с этим руководством команда разработчиков компании Contoso хочет добавить поддержку для цифровых подписей в приложение Contoso расходы. UWP **InkCanvas** управления это отличный вариант для этого сценария, так как он поддерживает рукописный ввод и Искусственного интеллекта функции, такие как возможность распознавания текста и фигур. Чтобы сделать это, понадобится [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) оболочку управления универсальной платформы Windows, доступные в наборе средств сообщества Windows. Этот элемент управления создает оболочку для интерфейса и функциональных возможностей универсальной платформы Windows **InkCanvas** элемент управления для использования в приложении WPF. Дополнительные сведения о оболочку управления универсальной платформы Windows, см. в разделе [управляет XAML UWP узла в классических приложениях (XAML о-ва)](xaml-islands.md).

## <a name="configure-the-project-to-use-xaml-islands"></a>Настройка проекта для использования XAML о-ва

Перед добавлением **InkCanvas** элемента управления в приложение Contoso расходы, необходимо сначала настроить проект для поддержки о-ва XAML UWP.

1. В Visual Studio 2019 г., щелкните правой кнопкой мыши **ContosoExpenses.Core** в проекте **обозревателе решений** и выберите **управление пакетами NuGet**.

    ![Управление пакетами NuGet в меню Visual Studio](images/wpf-modernize-tutorial//ManageNuGetPackages.png)

2. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Выберите **включить предварительные выпуски** параметр, поиск `Microsoft.Toolkit.Wpf.UI.Controls` упаковывать и установить последний выпуск предварительной версии пакета в результатах.

    > [!NOTE]
    > Этот пакет содержит всю необходимую инфраструктуру для размещения острова XAML UWP в приложении WPF, включая **InkCanvas** оболочку управления универсальной платформы Windows. Аналогичный пакет с именем `Microsoft.Toolkit.Forms.UI.Controls` доступен для приложений Windows Forms.

3. Щелкните правой кнопкой мыши **ContosoExpenses.Core** в проекте **обозревателе решений** и выберите **Добавить -> новый элемент**.

4. Выберите **файл манифеста приложения**, назовите его **app.manifest**и нажмите кнопку **добавить**.

5. В открытом файле манифеста найдите **совместимости** разделе и определите следующие комментарии **supportedOS** элемент для Windows 10.

    ```xml
    <!-- Windows 10 -->
    <!--<supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />-->
    ```

6. Расположенный под этим элементом, добавьте следующий **maxversiontested укажите** элемент.

    ```xml
    <maxversiontested Id="10.0.18362.0"/>
    ```

7. Раскомментируйте **supportedOS** элемент для Windows 10. В этом разделе теперь должна выглядеть следующим образом.

    ```xml
    <!-- Windows 10 -->
    <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
    <maxversiontested Id="10.0.18362.0"/>
    ```

    > [!NOTE]
    > **Maxversiontested укажите** элемент указывает, что приложению требуется Windows 10 версии 1903 (сборка 18362) или более поздней версии. Это первая версия Windows 10, который поддерживает острова XAML. Без этой записи в манифесте приложения приложения приведет к возникновению исключения во время выполнения. После добавления этого элемента может появиться следующее предупреждение построения в проекте: `manifest authoring warning 81010002: Unrecognized Element "maxversiontested" in namespace "urn:schemas-microsoft-com:compatibility.v1"`. Это предупреждение указывает, что-то не так в проекте, что его можно пропустить.

8. В файле манифеста найдите следующие комментарии **приложения** раздел.

    ```xml
    <!--
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
        <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true</dpiAware>
      </windowsSettings>
    </application>
    -->
    ```

9. Удалите этот раздел и замените его следующим кодом XML. Это позволит настроить приложение полностью точек на ДЮЙМ, поддерживающих и лучше дескриптор разные коэффициенты масштабирования поддерживается в Windows 10.

    ```xml
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
          <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/PM</dpiAware>
          <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2, PerMonitor</dpiAwareness>
      </windowsSettings>
    </application>
    ```

10. Сохраните и закройте `app.manifest` файл.

12. В **обозревателе решений**, щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **свойства**.

13. В **ресурсы** раздел **приложения** вкладке, убедитесь, что **манифеста** раскрывающийся список будет присвоено **app.manifest**.

    ![Манифест приложения .NET core](images/wpf-modernize-tutorial/NetCoreAppManifest.png)

16. Сохраните изменения в свойствах проекта.

## <a name="add-an-inkcanvas-control-to-the-app"></a>Добавление в приложение элемент управления InkCanvas

Теперь, когда вы настроили проект для использования XAML острова универсальной платформы Windows, теперь вы готовы добавить [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) оболочку управления универсальной платформы Windows в приложение.

1. В **обозревателе решений**, разверните **представления** папке **ContosoExpenses.Core** проекта и дважды щелкните **ExpenseDetail.xaml**файл.

2. В **окно** элемент в верхней части файла XAML, добавьте следующий атрибут. Это ссылается на пространство имен XAML для [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) оболочку управления универсальной платформы Windows.

    ```xml
    xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    После добавления этого атрибута **окно** элемент теперь должен выглядеть следующим образом.

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

4. В **ExpenseDetail.xaml** файл, найдите закрывающий `</Grid>` тег, который непосредственно предшествует `<!-- Chart -->` комментарий. Добавьте следующий XAML непосредственно перед закрывающим `</Grid>` тега. Добавляет этот XAML **InkCanvas** управления (с префиксом **toolkit** ключевое слово, определенную ранее как пространство имен) и простое **TextBlock** , выступающий в качестве заголовка для элемента управления.

    ```xml
    <TextBlock Text="Signature:" FontSize="16" FontWeight="Bold" Grid.Row="5" />

    <toolkit:InkCanvas x:Name="Signature" Grid.Row="6" />
    ```

5. Сохранить **ExpenseDetail.xaml** файла.

6. Нажмите клавишу F5, чтобы запустить приложение в отладчике.

7. Выберите сотрудник из списка и затем выберите один из доступных расходы. Обратите внимание, что страница сведений о расходах пространство для **InkCanvas** элемента управления.

    ![Только перо Canvas](images/wpf-modernize-tutorial/InkCanvasPenOnly.png)

    Если у вас есть устройство, которое поддерживает цифровое перо, аналогично области, и вы используете это лабораторное занятие на физическом компьютере, перейдите на и попытаться использовать его. Вы увидите цифровых рукописных данных, отображаемых на экране. Тем не менее если у вас нет устройство пера и попытке входа с помощью мыши, ничего не происходит. Это происходит потому, что **InkCanvas** только для цифровых перьев включен элемент управления по умолчанию. Тем не менее мы можем изменить это поведение.

8. Закройте приложение и дважды щелкните **ExpenseDetail.xaml.cs** файл **представления** папке **ContosoExpenses.Core** проекта.

9. Добавьте следующее объявление пространства имен в верхней части класса:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

10. Найдите `ExpenseDetail()` конструктор.

11. Добавьте следующую строку кода сразу после `InitializeComponent()` метод и сохраните файл кода.

    ```csharp
    Signature.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    Можно использовать **InkPresenter** объекта для настройки по умолчанию, рукописный ввод качества. Этот код использует **InputDeviceTypes** свойством для включения мыши, а также пера.

12. Нажмите клавишу F5, чтобы перестроить и запустить приложение в отладчике. Выберите сотрудник из списка и затем выберите один из доступных расходы.

13. Попробуйте сейчас нарисовать что-нибудь в пространстве подпись с помощью мыши. На этот раз вы увидите рукописного ввода, отображаемых на экране.

    ![Подпись](images/wpf-modernize-tutorial/Signature.png)

## <a name="next-steps"></a>Следующие шаги

На этом этапе в этом руководстве, вы успешно добавили UWP **InkCanvas** элемента управления в приложение Contoso расходы. Теперь вы готовы для [часть 3: Добавьте элемент управления представления календаря для универсальной платформы Windows, с помощью XAML о-ва](modernize-wpf-tutorial-3.md).

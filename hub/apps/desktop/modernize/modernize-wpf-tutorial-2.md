---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы XAML UWP, создавать пакеты MSIX и внедрять в приложение WPF другие современные компоненты.
title: Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands
ms.topic: article
ms.date: 08/15/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, о-ва XAML
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: fb7bb6d4e5af8992571f9740c1321e271b2e1672
ms.sourcegitcommit: 6bb794c6e309ba543de6583d96627fbf1c177bef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643417"
---
# <a name="part-2-add-a-uwp-inkcanvas-control-using-xaml-islands"></a>Часть 2. Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands

Это вторая часть учебника, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Общие сведения о руководстве, предварительных требованиях и инструкциях по скачиванию примера приложения см [. в разделе Учебник. Модернизировать приложение](modernize-wpf-tutorial.md)WPF. В этой статье предполагается, что вы уже выполнили [часть 1](modernize-wpf-tutorial-1.md).

В вымышленном сценарии в рамках этого руководства группа разработки Contoso хочет добавить поддержку цифровых подписей в приложение contoso "расходы". Элемент управления UWP **InkCanvas** является отличным вариантом для этого сценария, так как он поддерживает цифровые рукописные и AI-функции, такие как возможность распознавания текста и фигур. Для этого вы будете использовать элемент управления UWP, упакованный с помощью [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) , доступный в наборе средств сообщества Windows. Этот элемент управления заключает интерфейс и функциональные возможности элемента управления UWP **InkCanvas** для использования в приложении WPF. Дополнительные сведения о упакованных элементах управления UWP см. [в разделе Размещение элементов управления XAML в классических приложениях (острова XAML)](xaml-islands.md).

## <a name="configure-the-project-to-use-xaml-islands"></a>Настройка проекта для использования островов XAML

Прежде чем можно будет добавить элемент управления **InkCanvas** в приложение "расходы Contoso", сначала необходимо настроить проект для поддержки островов XAML UWP.

1. В Visual Studio 2019 щелкните правой кнопкой мыши проект **контосоекспенсес. Core** в **Обозреватель решений** и выберите **Управление пакетами NuGet**.

    ![Меню "Управление пакетами NuGet" в Visual Studio](images/wpf-modernize-tutorial//ManageNuGetPackages.png)

2. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**. Выберите параметр **включить предварительные** выпуски, найдите `Microsoft.Toolkit.Wpf.UI.Controls` пакет и установите последнюю предварительную версию пакета, показанного в результатах. Убедитесь, что установлена версия 6.0.0-preview7 или более поздняя версия.

    > [!NOTE]
    > Этот пакет содержит всю необходимую инфраструктуру для размещения островов XAML UWP в приложении WPF, включая элемент управления UWP, упакованный с помощью класса **InkCanvas** . Похожий пакет с `Microsoft.Toolkit.Forms.UI.Controls` именем доступен для приложений Windows Forms.

3. Щелкните правой кнопкой мыши проект **контосоекспенсес. Core** в **Обозреватель решений** и выберите **Добавить-> новый элемент**.

4. Выберите **файл манифеста приложения**, назовите его **app. manifest**и нажмите кнопку **добавить**. Дополнительные сведения о манифестах приложений см. в [этой статье](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests).

5. В файле манифеста раскомментируйте следующий `<supportedOS>` элемент для Windows 10.

    ```xml
    <!-- Windows 10 -->
    <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
    ```

6. В файле манифеста выберите следующий `<application>` элемент с комментарием.

    ```xml
    <!--
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
        <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true</dpiAware>
      </windowsSettings>
    </application>
    -->
    ```

7. Удалите этот раздел и замените его следующим XML-кодом. Это настраивает приложение на разрешение DPI и улучшает обработку различных факторов масштабирования, поддерживаемых Windows 10.

    ```xml
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
      <windowsSettings>
          <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/PM</dpiAware>
          <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2, PerMonitor</dpiAwareness>
      </windowsSettings>
    </application>
    ```

8. Сохраните и закройте `app.manifest` файл.

9. В **Обозреватель решений**щелкните правой кнопкой мыши проект **контосоекспенсес. Core** и выберите пункт **Свойства**.

10. В разделе **ресурсы** на вкладке **приложение** убедитесь, что для раскрывающегося списка **манифеста** задано значение **app. manifest**.

    ![Манифест приложения .NET Core](images/wpf-modernize-tutorial/NetCoreAppManifest.png)

11. Сохраните изменения в свойствах проекта.

## <a name="add-an-inkcanvas-control-to-the-app"></a>Добавление элемента управления InkCanvas в приложение

Теперь, когда вы настроили проект для использования островов XAML UWP, теперь все готово к добавлению в приложение элемента управления UWP, упакованного с помощью класса [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) .

1. В **Обозреватель решений**разверните папку **представления** проекта **контосоекспенсес. Core** и дважды щелкните файл **експенседетаил. XAML** .

2. В элементе **Window** в верхней части файла XAML добавьте следующий атрибут. Он ссылается на пространство имен XAML для элемента управления UWP, упакованного в объект с кодом [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) .

    ```xml
    xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    После добавления этого атрибута элемент **Window** теперь должен выглядеть следующим образом.

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

4. В файле **експенседетаил. XAML** нахождение закрывающего `</Grid>` тега, `<!-- Chart -->` непосредственно предшествующего комментарию. Добавьте следующий код XAML непосредственно перед закрывающим `</Grid>` тегом. Этот XAML добавляет элемент управления **InkCanvas** (с префиксом, определенным ранее в качестве пространства имен) и простой **TextBlock** , который выступает в качестве заголовка для элемента управления.

    ```xml
    <TextBlock Text="Signature:" FontSize="16" FontWeight="Bold" Grid.Row="5" />

    <toolkit:InkCanvas x:Name="Signature" Grid.Row="6" />
    ```

5. Сохраните файл **експенседетаил. XAML** .

6. Нажмите клавишу F5, чтобы запустить приложение в отладчике.

7. Выберите сотрудника из списка, а затем выберите один из доступных расходов. Обратите внимание, что страница сведения о расходах содержит пространство для элемента управления **InkCanvas** .

    ![Только перо холста для рукописного ввода](images/wpf-modernize-tutorial/InkCanvasPenOnly.png)

    Если у вас есть устройство, которое поддерживает цифровое перо, например поверхность, и вы используете этот семинар на физическом компьютере, перейдите к разделу и попытайтесь использовать его. На экране будут отображаться рукописные данные. Однако если у вас нет устройства, поддерживающего перо, и вы пытаетесь подписаться с помощью мыши, ничего не произойдет. Это происходит потому, что элемент управления **InkCanvas** включен по умолчанию только для цифровых перьев. Однако это поведение можно изменить.

8. Закройте приложение и дважды щелкните файл **ExpenseDetail.XAML.CS** в папке Views проекта **контосоекспенсес. Core** .

9. Добавьте следующее объявление пространства имен в начало класса:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

10. Нахождение `ExpenseDetail()` конструктора.

11. Добавьте следующую строку кода после `InitializeComponent()` метода и сохраните файл кода.

    ```csharp
    Signature.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    Объект **InkPresenter** можно использовать для настройки интерфейса рукописного ввода по умолчанию. Этот код использует свойство **инпутдевицетипес** для включения мыши, а также для ввода с помощью пера.

12. Снова нажмите клавишу F5, чтобы перестроить и запустить приложение в отладчике. Выберите сотрудника из списка, а затем выберите один из доступных расходов.

13. Попробуйте нарисовать что-то в области подписи с помощью мыши. На этот раз на экране будут отображаться рукописные данные.

    ![Подпись](images/wpf-modernize-tutorial/Signature.png)

## <a name="next-steps"></a>Следующие шаги

На этом этапе работы с руководством вы успешно добавили элемент управления UWP **InkCanvas** в приложение "расходы Contoso". Теперь вы готовы [к части 3: Добавьте элемент управления UWP Календарвиев с помощью XAML](modernize-wpf-tutorial-3.md)-ва.

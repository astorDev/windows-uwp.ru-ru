---
ms.assetid: c43d4af3-9a1a-4eae-a137-1267c293c1b5
description: В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах.
title: Функции пользовательского интерфейса камеры для мобильных устройств
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a6e72aea74c4aed092cab450c05dc0982e838f09
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358971"
---
# <a name="camera-ui-features-for-mobile-devices"></a>Функции пользовательского интерфейса камеры для мобильных устройств

В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах. 

## <a name="add-the-mobile-extension-to-your-project"></a>Добавление в проект расширения для мобильных устройств 

Чтобы использовать эти функции, добавьте в проект ссылку на Microsoft Mobile Extension SDK для универсальной платформы приложений.

**Чтобы добавить ссылку на пакет SDK мобильных расширения аппаратную поддержку камеры кнопки**

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку**.

2.  Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.

3.  Установите флажок **Microsoft Desktop Extension SDK для универсальной платформы приложений**.

## <a name="hide-the-status-bar"></a>Скрытие строки состояния

На мобильных устройствах доступен элемент управления [**StatusBar**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.StatusBar), который предоставляет пользователю сведения о состоянии устройства. Этот элемент занимает место на экране, что может повлиять на возможности пользовательского интерфейса для захвата мультимедиа. Строку строка состояния можно скрыть с помощью метода [**HideAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.statusbar.hideasync), однако его следует вызывать из условного блока, в котором метод [**ApiInformation.IsTypePresent**](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.istypepresent) определяет доступность API. Этот метод возвратит значение true только на мобильных устройствах, которые поддерживают строку состояния. Строку состояния необходимо скрывать при запуске приложения и во время предварительного просмотра с камеры.

[!code-cs[HideStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHideStatusBar)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа вашего приложения, этот элемент управления можно снова сделать видимым.

[!code-cs[ShowStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetShowStatusBar)]

## <a name="use-the-hardware-camera-button"></a>Использование аппаратной кнопки камеры

На некоторых мобильных устройствах есть специальная кнопка камеры, которую некоторые пользователи применяют вместо элемента управления на экране. Чтобы получать уведомления о нажатии кнопки камеры, зарегистрируйте обработчик события [**HardwareButtons.CameraPressed**](https://docs.microsoft.com/uwp/api/windows.phone.ui.input.hardwarebuttons.camerapressed). Поскольку этот API доступен только на мобильных устройствах, перед попыткой получить к нему доступ необходимо снова использовать **IsTypePresent**, чтобы убедиться, что API поддерживается на текущем устройстве.

[!code-cs[PhoneUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPhoneUsing)]

[!code-cs[RegisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterCameraButtonHandler)]

В обработчике события **CameraPressed** можно начать фотозахват.

[!code-cs[CameraPressed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCameraPressed)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа, отмените регистрацию обработчика кнопки.

[!code-cs[UnregisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUnregisterCameraButtonHandler)]

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)






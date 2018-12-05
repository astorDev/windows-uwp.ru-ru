---
ms.assetid: c43d4af3-9a1a-4eae-a137-1267c293c1b5
description: В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах.
title: Функции пользовательского интерфейса камеры для мобильных устройств
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1bf27de9c9b1bce2b35918b2a9d1357d2f3ba20b
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8713701"
---
#<a name="camera-ui-features-for-mobile-devices"></a>Функции пользовательского интерфейса камеры для мобильных устройств

В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах. 

## <a name="add-the-mobile-extension-to-your-project"></a>Добавление в проект расширения для мобильных устройств 

Чтобы использовать эти функции, добавьте в проект ссылку на Microsoft Mobile Extension SDK для универсальной платформы приложений.

**Как добавить ссылку на мобильный SDK расширения для поддержки кнопки камеры**

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку**.

2.  Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.

3.  Установите флажок **Microsoft Desktop Extension SDK для универсальной платформы приложений**.

## <a name="hide-the-status-bar"></a>Скрытие строки состояния

На мобильных устройствах доступен элемент управления [**StatusBar**](https://msdn.microsoft.com/library/windows/apps/dn633864), который предоставляет пользователю сведения о состоянии устройства. Этот элемент занимает место на экране, что может повлиять на возможности пользовательского интерфейса для захвата мультимедиа. Строку строка состояния можно скрыть с помощью метода [**HideAsync**](https://msdn.microsoft.com/library/windows/apps/dn610339), однако его следует вызывать из условного блока, в котором метод [**ApiInformation.IsTypePresent**](https://msdn.microsoft.com/library/windows/apps/dn949016) определяет доступность API. Этот метод возвратит значение true только на мобильных устройствах, которые поддерживают строку состояния. Строку состояния необходимо скрывать при запуске приложения и во время предварительного просмотра с камеры.

[!code-cs[HideStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHideStatusBar)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа вашего приложения, этот элемент управления можно снова сделать видимым.

[!code-cs[ShowStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetShowStatusBar)]

## <a name="use-the-hardware-camera-button"></a>Использование аппаратной кнопки камеры

На некоторых мобильных устройствах есть специальная кнопка камеры, которую некоторые пользователи применяют вместо элемента управления на экране. Чтобы получать уведомления о нажатии кнопки камеры, зарегистрируйте обработчик события [**HardwareButtons.CameraPressed**](https://msdn.microsoft.com/library/windows/apps/dn653805). Поскольку этот API доступен только на мобильных устройствах, перед попыткой получить к нему доступ необходимо снова использовать **IsTypePresent**, чтобы убедиться, что API поддерживается на текущем устройстве.

[!code-cs[PhoneUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPhoneUsing)]

[!code-cs[RegisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterCameraButtonHandler)]

В обработчике события **CameraPressed** можно начать фотозахват.

[!code-cs[CameraPressed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCameraPressed)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа, отмените регистрацию обработчика кнопки.

[!code-cs[UnregisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUnregisterCameraButtonHandler)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)






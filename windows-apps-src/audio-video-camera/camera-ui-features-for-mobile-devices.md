---
author: drewbatgit
ms.assetid: 
description: "В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах."
title: "Функции пользовательского интерфейса камеры для мобильных устройств"
translationtype: Human Translation
ms.sourcegitcommit: 77d1709cd42253c229b01df21ae3416e57c1c2ab
ms.openlocfilehash: ec437d7111b1490f52bfc53b3ad2cd06f0c66ef3

---

#Функции пользовательского интерфейса камеры для мобильных устройств

В этой статье показано, как использовать особенные элементы пользовательского интерфейса камеры, которые присутствуют только на мобильных устройствах. 

## Добавление в проект расширения для мобильных устройств 

Чтобы использовать эти функции, добавьте в проект ссылку на Microsoft Mobile Extension SDK для универсальной платформы приложений.

**Как добавить ссылку на мобильный SDK расширения для поддержки кнопки камеры**

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку**.

2.  Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.

3.  Установите флажок **Microsoft Desktop Extension SDK для универсальной платформы приложений**.

## Скрытие строки состояния

На мобильных устройствах доступен элемент управления [**StatusBar**](https://msdn.microsoft.com/library/windows/apps/dn633864), который предоставляет пользователю сведения о состоянии устройства. Этот элемент занимает место на экране, что может повлиять на возможности пользовательского интерфейса для захвата мультимедиа. Строку строка состояния можно скрыть с помощью метода [**HideAsync**](https://msdn.microsoft.com/library/windows/apps/dn610339), однако его следует вызывать из условного блока, в котором метод [**ApiInformation.IsTypePresent**](https://msdn.microsoft.com/library/windows/apps/dn949016) определяет доступность API. Этот метод возвратит значение true только на мобильных устройствах, которые поддерживают строку состояния. Строку состояния необходимо скрывать при запуске приложения и во время предварительного просмотра с камеры.

[!code-cs[HideStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHideStatusBar)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа вашего приложения, этот элемент управления можно снова сделать видимым.

[!code-cs[ShowStatusBar](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetShowStatusBar)]

## Использование аппаратной кнопки камеры

На некоторых мобильных устройствах есть специальная кнопка камеры, которую некоторые пользователи применяют вместо элемента управления на экране. Чтобы получать уведомления о нажатии кнопки камеры, зарегистрируйте обработчик события [**HardwareButtons.CameraPressed**](https://msdn.microsoft.com/library/windows/apps/dn653805). Поскольку этот API доступен только на мобильных устройствах, перед попыткой получить к нему доступ необходимо снова использовать **IsTypePresent**, чтобы убедиться, что API поддерживается на текущем устройстве.

[!code-cs[PhoneUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPhoneUsing)]

[!code-cs[RegisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterCameraButtonHandler)]

В обработчике события **CameraPressed** можно начать фотозахват.

[!code-cs[CameraPressed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCameraPressed)]

Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа, отмените регистрацию обработчика кнопки.

[!code-cs[UnregisterCameraButtonHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUnregisterCameraButtonHandler)]

> [!NOTE]
> Эта статья адресована разработчикам приложений для Windows10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).                                                                                   |

## Связанные разделы

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)








<!--HONumber=Aug16_HO3-->



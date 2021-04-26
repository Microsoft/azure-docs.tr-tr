---
title: FIDO2 parolasız kimlik doğrulamasının tarayıcı desteği | Azure Active Directory
description: Tarayıcılar ve işletim sistemi birleşimleri Azure Active Directory kullanarak uygulamalar için FIDO2 parolasız kimlik doğrulamasını destekler
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632110"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 passwordless kimlik doğrulamasının tarayıcı desteği

Azure Active Directory, [FIDO2 güvenlik anahtarlarının](./concept-authentication-passwordless.md#fido2-security-keys) parolasız bir cihaz olarak kullanılmasına izin verir. Microsoft hesapları için FIDO2 kimlik doğrulamasının kullanılabilirliği [2018 ' de duyurulmuştur](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Duyuruda anlatıldığı gibi, Microsoft ve Azure Active Directory hesaplarıyla güvenli kimlik doğrulamayı desteklemek için, FIDO2 CTAP belirtimine yönelik bazı isteğe bağlı özellikler ve uzantılar uygulanmalıdır. Aşağıdaki diyagramda, Azure Active Directory ile FIDO2 kimlik doğrulama anahtarlarını kullanarak parolasız kimlik doğrulamasını destekleyen tarayıcıların ve işletim sistemi birleşimleri gösterilmektedir.

## <a name="supported-browsers"></a>Desteklenen tarayıcılar

Bu tabloda Azure Active Directory (Azure AD) ve Microsoft hesaplarının (MSA) kimlik doğrulaması desteği gösterilmektedir. Microsoft hesapları, müşteriler tarafından Xbox, Skype veya Outlook.com gibi hizmetler için oluşturulur. Desteklenen cihaz türleri arasında **USB**, yakın alan Iletişimi (**NFC**) ve Bluetooth düşük enerji (**ble**) bulunur.

| İşletim Sistemi | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | Aya | USB | NFC | Aya | USB | NFC | Aya |
| **Windows**  | ![Chrome, AAD için Windows için USB hesaplarını destekler.][y] | ![Chrome, AAD için Windows hesaplarında NFC 'yi destekler.][y] | ![Chrome, AAD hesaplarında Windows üzerinde BLE 'ı destekler.][y] | ![Edge, AAD için Windows hesaplarında USB 'yi destekler.][y] | ![Edge, AAD için Windows hesaplarında NFC 'yi destekler.][y] | ![Edge, AAD hesaplarında Windows üzerinde BLE 'ı destekler.][y] | ![Firefox, AAD için Windows için USB hesaplarını destekler.][y] | ![Firefox, AAD için Windows hesaplarında NFC 'yi destekler.][y] | ![Firefox, AAD hesaplarında Windows üzerinde BLE 'ı destekler.][y] |
| **macOS**  | ![Chrome, AAD hesapları için macOS 'ta USB 'yi destekler.][y] | ![Chrome, AAD hesapları için macOS 'ta NFC 'yi desteklemez.][n] | ![Chrome, AAD hesapları için macOS üzerinde BLE 'ı desteklemez.][n] | ![Edge, AAD hesapları için macOS 'ta USB 'yi destekler.][y] | ![Edge, AAD hesapları için macOS üzerinde NFC 'yi desteklemez.][n] | ![Edge, AAD hesapları için macOS üzerinde BLE 'ı desteklemiyor.][n] | ![Firefox, AAD hesapları için macOS üzerinde USB 'yi desteklemez.][n] | ![Firefox, AAD hesapları için macOS üzerinde NFC 'yi desteklemez.][n] | ![Firefox, AAD hesapları için macOS üzerinde BLE 'ı desteklemez.][n] |
| **Linux**  | ![Chrome, AAD hesapları için Linux üzerinde USB 'yi destekler.][y] | ![Chrome, AAD hesapları için Linux üzerinde NFC 'yi desteklemez.][n] | ![Chrome, AAD hesapları için Linux üzerinde BLE 'ı desteklemez.][n] | ![Edge, AAD hesapları için Linux üzerinde USB 'yi desteklemez.][n] | ![Edge, AAD hesapları için Linux üzerinde NFC 'yi desteklemez.][n] | ![Edge, AAD hesapları için Linux üzerinde BLE 'ı desteklemez.][n] | ![Firefox, AAD hesapları için Linux üzerinde USB 'yi desteklemez.][n] | ![Firefox, AAD hesapları için Linux üzerinde NFC 'yi desteklemez.][n] | ![Firefox, AAD hesapları için Linux üzerinde BLE 'ı desteklemez.][n] |



## <a name="unsupported-browsers"></a>Desteklenmeyen tarayıcılar

Aşağıdaki işletim sistemi ve tarayıcı birleşimleri desteklenmez, ancak gelecekteki destek ve test araştırılmakta. Diğer işletim sistemi ve tarayıcı desteğini görmek isterseniz, lütfen sayfanın altındaki ürün geri bildirim aracını kullanarak geri bildirimde bulunun.

| İşletim sistemi | Tarayıcı |
| ---- | ---- |
| iOS | Safari, brave |
| Mac OS | Safari |
| Android | Chrome |
| Kmeos | Chrome |

## <a name="minimum-browser-version"></a>En düşük tarayıcı sürümü

En düşük tarayıcı sürümü gereksinimleri aşağıda verilmiştir. 

| Tarayıcı | En düşük sürüm |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 sürüm 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup> Yeni Kmıum tabanlı Microsoft Edge support Fido2 'ın tüm sürümleri. Microsoft Edge için eski destek 1903 ' ye eklenmiştir.

## <a name="next-steps"></a>Sonraki adımlar
[Passwordless güvenlik anahtarı oturumunu etkinleştir (Önizleme)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png

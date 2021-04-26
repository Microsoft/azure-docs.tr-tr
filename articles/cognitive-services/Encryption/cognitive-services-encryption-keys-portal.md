---
title: Bilişsel hizmetler için Customer-Managed anahtarları
titleSuffix: Cognitive Services
description: Azure portal kullanarak müşteri tarafından yönetilen anahtarları Azure Key Vault ile yapılandırma hakkında bilgi edinin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 7c7476a3ab885e9c127cbd571ad723864bf0d898
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534556"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Bilişsel hizmetler için Azure Key Vault müşteri tarafından yönetilen anahtarları yapılandırın

Bilişsel hizmetler için Azure Key Vault Customer-Managed anahtarları etkinleştirme işlemi ürüne göre farklılık gösterir. Hizmete özgü yönergeler için bu bağlantıları kullanın:

## <a name="vision"></a>Görsel

* [Bekleyen verilerin şifrelenmesi Özel Görüntü İşleme](../custom-vision-service/encrypt-data-at-rest.md)
* [Yüz Hizmetleri, bekleyen verilerin şifrelenmesi](../face/encrypt-data-at-rest.md)
* [Bekleyen verilerin form tanıyıcı şifrelemesini şifreleme](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Dil

* [Bekleyen verilerin hizmet şifrelemesini Language Understanding](../LUIS/encrypt-data-at-rest.md)
* [Bekleyen verilerin şifrelenmesi Soru-Cevap Oluşturma](../QnAMaker/encrypt-data-at-rest.md)
* [Bekleyen verilerin Translator şifrelemesi](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>Konuşma

* [Bekleyen verilerin konuşma şifrelemesi](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>Karar

* [Bekleyen verilerin şifrelenmesi Content Moderator](../Content-Moderator/encrypt-data-at-rest.md)
* [Bekleyen verilerin kişiselleştirici şifrelemesi](../personalizer/encrypt-data-at-rest.md)
* [Bekleyen verilerin ölçüm Danışmanı şifrelemesi](../metrics-advisor/encryption.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Key Vault nedir](../../key-vault/general/overview.md)?
* [Bilişsel hizmetler Customer-Managed anahtar Isteği formu](https://aka.ms/cogsvc-cmk)
---
title: Bekleyen verilerin şifrelenmesi Özel Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Microsoft, Microsoft tarafından yönetilen şifreleme anahtarları sunar ve ayrıca bilişsel hizmetler aboneliklerinizi, müşteri tarafından yönetilen anahtarlar (CMK) olarak adlandırılan kendi Anahtarlarınıza göre yönetmenizi sağlar. Bu makalede, Özel Görüntü İşleme için bekleyen veri şifrelemesi ve CMK 'nin nasıl etkinleştirileceği ve yönetileceği ele alınmaktadır.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652267"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Bekleyen verilerin şifrelenmesi Özel Görüntü İşleme

Azure Özel Görüntü İşleme, verileri buluta kalıcı hale geldiğinde otomatik olarak şifreler. Özel Görüntü İşleme şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar yalnızca 11 Mayıs 2020 ' den sonra oluşturulan kullanılabilir kaynaklardır. CMK 'yi Özel Görüntü İşleme kullanmak için yeni bir Özel Görüntü İşleme kaynağı oluşturmanız gerekir. Kaynak oluşturulduktan sonra, yönetilen kimliğinizi ayarlamak için Azure Key Vault kullanabilirsiniz.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Sonraki adımlar

* CMK 'yı destekleyen hizmetlerin tam listesi için bkz. bilişsel [Hizmetler Için müşteri tarafından yönetilen anahtarlar](../encryption/cognitive-services-encryption-keys-portal.md)
* [Azure Key Vault nedir](../../key-vault/general/overview.md)?
* [Bilişsel hizmetler Customer-Managed anahtar Isteği formu](https://aka.ms/cogsvc-cmk)
---
title: Bekleyen veriler için yüz hizmeti şifreleme
titleSuffix: Azure Cognitive Services
description: Microsoft, Microsoft tarafından yönetilen şifreleme anahtarları sunar ve ayrıca bilişsel hizmetler aboneliklerinizi, müşteri tarafından yönetilen anahtarlar (CMK) olarak adlandırılan kendi Anahtarlarınıza göre yönetmenizi sağlar. Bu makalede, yüz için bekleyen veri şifreleme ve CMK 'yi etkinleştirme ve yönetme konuları ele alınmaktadır.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650822"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Bekleyen veriler için yüz hizmeti şifreleme

Yüz hizmeti, buluta kalıcı yapıldığında verilerinizi otomatik olarak şifreler. Yüz hizmeti şifrelemesi verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar yalnızca E0 fiyatlandırma katmanında kullanılabilir. Müşteri tarafından yönetilen anahtarları kullanma olanağı istemek için [yüz hizmeti Customer-Managed anahtar Isteği formunu](https://aka.ms/cogsvc-cmk)doldurun ve iletin. İsteğinizin durumunu öğrenmek yaklaşık 3-5 iş günü sürer. Talebe bağlı olarak, bir kuyruğa yerleştirilmiş ve alan kullanılabilir olduğunda onaylanmış olabilir. Yüz hizmeti ile CMK kullanmaya onaylandıktan sonra, yeni bir yüz kaynağı oluşturmanız ve fiyatlandırma katmanı olarak E0 seçmeniz gerekir. E0 fiyatlandırma katmanı ile yüz kaynağınız oluşturulduktan sonra, yönetilen kimliğinizi ayarlamak için Azure Key Vault kullanabilirsiniz.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Sonraki adımlar

* CMK 'yı destekleyen hizmetlerin tam listesi için bkz. bilişsel [Hizmetler Için müşteri tarafından yönetilen anahtarlar](../encryption/cognitive-services-encryption-keys-portal.md)
* [Azure Key Vault nedir](../../key-vault/general/overview.md)?
* [Bilişsel hizmetler Customer-Managed anahtar Isteği formu](https://aka.ms/cogsvc-cmk)

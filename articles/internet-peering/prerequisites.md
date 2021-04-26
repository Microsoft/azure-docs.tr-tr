---
title: Microsoft ile eşleme ayarlama önkoşulları
titleSuffix: Azure
description: Microsoft ile eşleme ayarlama önkoşulları
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586849"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Microsoft ile eşleme ayarlama önkoşulları

Yeni bir eşleme istemesi veya eski bir eşlemeyi Azure kaynağına dönüştürmek için aşağıdaki önkoşulların karşılandığından emin olun.

## <a name="azure-related-prerequisites"></a>Azure ile ilgili Önkoşullar
* **Microsoft Azure hesabı:** Microsoft Azure hesabınız yoksa, bir [Microsoft Azure hesabı](https://azure.microsoft.com/free)oluşturun. Eşleme ayarlamak için geçerli ve etkin bir Microsoft Azure abonelik gerekir, çünkü eşlemeler Azure abonelikleri içinde kaynak olarak modellenir. Şunlara dikkat edilmesi önemlidir:
    * Eşleme ayarlamak için kullanılan Azure Kaynak türleri, her zaman ücretsiz Azure ürünlerdir, yani Azure hesabı oluşturmak veya abonelik oluşturmak veya eşleme ayarlamak için Azure kaynaklarına **Peerasn** ve **eşleme** ile erişmek için ücretlendirilirsiniz. Bu, sizin ve Microsoft arasındaki doğrudan eşleme, eşleme takımımız tarafından açıkça ele alınan hüküm için eşleme sözleşmesi ile karıştırılmamalıdır. Bu tür sorularınız varsa, [Microsoft eşleme](mailto:peering@microsoft.com) ile iletişim kurun.
    * Ücretsiz veya ücretli olabilecek diğer Azure ürünlerine veya bulut hizmetlerine erişmek için aynı Azure aboneliğini kullanabilirsiniz. Ücretli bir ürüne eriştiğinizde ücretlendirilecektir.
    * Yeni bir Azure hesabı ve/veya aboneliği oluşturuyorsanız, Azure bulut hizmetleri 'ni denemek için kullanabileceğiniz bir deneme süresi boyunca ücretsiz Azure kredisine uygun olabilirsiniz. İlgileniyorsa daha fazla bilgi için [Microsoft Azure hesabını](https://azure.microsoft.com/free) ziyaret edin.

* **Eş ASN 'Yi ilişkilendir:** Eşleme isteğinde bulunulmadan önce, ASN ve iletişim bilgilerinizi aboneliğiniz ile ilişkilendirin. [EŞDÜZEY ASN 'Yi Azure aboneliğine ilişkilendir](howto-subscription-association-powershell.md)bölümündeki yönergeleri izleyin.

## <a name="other-prerequisites"></a>Diğer Önkoşullar
* **Peeringdb profili:** Eşlerin [Peeringdb](https://www.peeringdb.com)'de tam ve güncel bir profili olması beklenir. Bu bilgileri kayıt sistemimizde, NOC bilgileri, teknik iletişim bilgileri ve bunların eşleme tesislerinde bulunan mevcut oldukları ayrıntılarını doğrulamak için kullanırız.

## <a name="next-steps"></a>Sonraki adımlar

* [Portalı kullanarak doğrudan eşleme oluşturun veya değiştirin](howto-direct-portal.md).
* [Portalı kullanarak eski bir Doğrudan eşlemeyi bir Azure kaynağına dönüştürme](howto-legacy-direct-portal.md)
* [Portalı kullanarak Exchange eşlemesi oluşturma veya değiştirme](howto-exchange-portal.md)
* [Portalı kullanarak eski bir Exchange eşlemesini bir Azure kaynağına dönüştürme](howto-legacy-exchange-portal.md)
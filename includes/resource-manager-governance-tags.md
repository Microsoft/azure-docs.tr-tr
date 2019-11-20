---
title: include dosyası
description: include dosyası
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199153"
---
Verileri bir taksonomiye mantıksal olarak düzenlemek için meta veriler sunarak Azure kaynaklarınıza Etiketler uygularsınız. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

Etiketleri uyguladıktan sonra aboneliğinizde bu etiket adını ve değerini taşıyan tüm kaynakları alabilirsiniz. Etiketler, farklı kaynak gruplarından ilgili kaynakları almanızı sağlar. Bu yaklaşım, faturalama veya yönetim için kaynakları düzenlemeniz gerektiğinde yararlıdır.

Taksonominiz, kullanıcıların yükünü azaltmak ve doğruluğu artırmak için otomatik etiketleme stratejisine ek olarak bir self servis meta veri etiketleme stratejisini göz önünde bulundurmalıdır.

Etiketler için aşağıdaki sınırlamalar geçerlidir:

* Tüm kaynak türleri etiketleri desteklemez. Bir kaynak türüne etiket uygulayıp uygulayacağınızı öğrenmek için bkz. [Azure kaynakları Için etiket desteği](../articles/azure-resource-manager/tag-support.md).
* Her kaynak veya kaynak grubu en fazla 50 etiket adı/değer çiftine sahip olabilir. İzin verilen maksimum sayıdan daha fazla etiket uygulamanız gerekiyorsa, etiket değeri için bir JSON dizesi kullanın. JSON dizesi, tek etiket adına uygulanan birden fazla değer içerebilir. Bir kaynak grubu, her birinin 50 etiket adı/değer çifti olan çok sayıda kaynak içerebilir.
* Etiket adı 512 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır. Depolama hesapları için etiket adı 128 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır.
* Genelleştirilmiş VM 'Ler etiketleri desteklemez.
* Kaynak grubuna uygulanan etiketler, bu kaynak grubundaki kaynaklar tarafından devralınmaz.
* Etiketler, Cloud Services gibi klasik kaynaklara uygulanamaz.
* Etiket adları şu karakterleri içeremez: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Şu anda Azure DNS bölgeler ve trafik yöneticisi hizmetleri de etikette boşluk kullanılmasına izin vermez. 

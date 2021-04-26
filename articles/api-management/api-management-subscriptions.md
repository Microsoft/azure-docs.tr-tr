---
title: Azure API Management abonelikler | Microsoft Docs
description: Azure API Management abonelik kavramı hakkında bilgi edinin. Tüketiciler Azure API Management abonelikler kullanarak API 'lere erişim sağlar.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511255"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management'ta abonelikler

Abonelikler Azure API Management 'da önemli bir kavramdır. Bu kişiler, API tüketicilerinin bir API Management örneği aracılığıyla yayımlanan API 'lere erişim alması için en yaygın yoldur. Bu makalede, kavrama genel bakış sunulmaktadır.

## <a name="what-are-subscriptions"></a>Abonelikler nelerdir?

API 'Leri API Management aracılığıyla yayımladığınızda, abonelik anahtarları kullanılarak bu API 'lere güvenli bir şekilde erişmek çok kolay ve yaygındır. Yayımlanmış API 'leri kullanması gereken geliştiriciler, bu API 'lere çağrılar yaptıklarında HTTP isteklerinde geçerli bir abonelik anahtarı içermelidir. Aksi takdirde, çağrılar API Management ağ geçidi tarafından hemen reddedilir. Bunlar arka uç hizmetlerine iletilmez.

API 'Lere erişim için bir abonelik anahtarı almak üzere bir abonelik gerekir. Abonelik temel olarak bir dizi abonelik anahtarı için adlandırılmış bir kapsayıcıdır. Yayımlanmış API 'Leri kullanması gereken geliştiriciler abonelikleri alabilir. Ve API yayımcılarından onay gerekmez. API yayımcıları, doğrudan API tüketicileri için abonelikler de oluşturabilir.

> [!TIP]
> API Management, aşağıdaki örnekler de dahil olmak üzere API 'lere erişimin güvenliğini sağlamak için diğer mekanizmaları da destekler:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [İstemci sertifikaları](api-management-howto-mutual-certificates-for-clients.md)
> - [Çağıran IP 'Leri kısıtla](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Aboneliklerin kapsamı

Abonelikler çeşitli kapsamlarla ilişkilendirilebilir: ürün, tüm API 'Ler veya tek bir API.

### <a name="subscriptions-for-a-product"></a>Bir ürün için abonelikler

Geleneksel olarak, API Management içindeki abonelikler her zaman tek bir [API ürün](api-management-terminology.md) kapsamıyla ilişkilendirilir. Geliştiriciler, geliştirici portalındaki ürünlerin listesini buldu. Ardından, kullanmak istedikleri ürünler için abonelik istekleri gönderir. Abonelik isteği onaylandıktan sonra, otomatik olarak veya API yayımcıları tarafından, geliştirici üründeki tüm API 'Lere erişmek için içindeki anahtarları kullanabilir. Geliştirici portalı, mevcut olduğunda yalnızca ürün kapsamı aboneliklerini Kullanıcı profili bölümünde gösterir. 

![Ürün abonelikleri](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Bazı senaryolarda, API yayımcıları abonelik gereksinimi olmadan bir API ürününü herkese yayımlamak isteyebilir. Azure portal ürünün **Ayarlar** sayfasında **abonelik gerektir** seçeneğinin işaretini kaldırabilir. Sonuç olarak, ürün kapsamındaki tüm API 'Ler bir API anahtarı olmadan erişilebilir.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Tüm API 'Ler veya tek bir API için abonelikler

API Management [Tüketim](https://aka.ms/apimconsumptionblog) katmanını sunduğumuz zaman, anahtar yönetimini kolaylaştırmak için birkaç değişiklik yaptık:
- İlk olarak, iki tane daha abonelik kapsamı ekledik: tüm API 'Ler ve tek bir API. Aboneliklerin kapsamı artık bir API ürünüyle sınırlı değildir. Artık bir API 'ye veya bir API Management örneğindeki tüm API 'lere erişim veren anahtarlar oluşturmak ve öncelikle bir ürün oluşturup API 'Leri eklemek mümkün değildir. Ayrıca, her bir API Management örneği artık sabit ve tüm API abonelikleri ile birlikte gelir. Bu abonelik test konsolundaki API 'Lerin test ve hata ayıklama işlemlerini kolaylaştırır ve daha basit hale getirir.

- İkincisi, API Management artık **tek başına** aboneliklerine izin veriyor. Aboneliklerin bir geliştirici hesabıyla ilişkilendirilmesi artık gerekli değildir. Bu özellik, birkaç geliştirici veya ekibin bir aboneliği paylaştığı durumlarda olduğu gibi senaryolarda faydalıdır.

- Son olarak, API yayımcıları artık Azure portal doğrudan [abonelik oluşturabilir](api-management-howto-create-subscriptions.md) :

    ![Esnek abonelikler](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Sonraki adımlar
API Management hakkında daha fazla bilgi alın:

+ API Management diğer [kavramları](api-management-terminology.md) öğrenin.
+ API Management hakkında daha fazla bilgi edinmek için [öğreticilerimizi](import-and-publish.md) izleyin.
+ Sık sorulan sorular için [SSS sayfamıza](api-management-faq.md) bakın.

---
title: Azure API Management dış önbellek kullanma | Microsoft Docs
description: Azure API Management 'de dış önbellek yapılandırmayı ve kullanmayı öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072496"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Azure 'da Redsıs için dış Azure önbelleği kullanın API Management

Azure API Management, yerleşik önbelleğin kullanılmasıyla ilgili ek olarak, bir dış Azure önbelleğindeki yanıtları redin için önbelleğe almaya da olanak tanır.

Dış önbelleğin kullanılması, yerleşik önbelleğin bazı sınırlamalarını aşmayı sağlar. Şunları yapmak istiyorsanız özellikle faydalıdır:

* API Management güncelleştirmeler sırasında önbelleğinizin düzenli aralıklarla silinmesini önleyin
* Önbellek yapılandırmanız üzerinde daha fazla denetime sahip
* API Management katmanından daha fazla veri önbelleğe almasına izin verir
* API Management tüketim katmanıyla önbelleğe alma kullanın

Önbelleğe alma hakkında daha ayrıntılı bilgi için bkz. [API Management önbelleğe alma ilkeleri](api-management-caching-policies.md) ve [Azure API Management'te özel önbelleğe alma](api-management-sample-cache-by-key.md).

![Kendi önbelleğinizi APıM 'e taşıyın](media/api-management-howto-cache-external/overview.png)

Öğrenecekleriniz:

> [!div class="checklist"]
> * API Management bir dış önbellek ekleyin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

+ [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
+ [Azure API Management önbelleğe almayı](api-management-howto-cache.md) anlama

## <a name="create-cache"></a> Redsıs Için Azure önbelleği oluşturma

Bu bölümde, Azure 'da redin için Azure önbelleğinin nasıl oluşturulacağı açıklanmaktadır. Zaten Azure 'un içinde veya dışında bir Azure önbelleğiniz varsa, sonraki bölüme <a href="#add-external-cache">atlayabilirsiniz</a> .

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Dış önbellek Ekle

Azure API Management 'de Redsıs için dış Azure önbelleği eklemek üzere aşağıdaki adımları izleyin.

![Kendi önbelleğinizi APıM 'e taşıyın](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **Içinden kullanım** ayarı, API Management çok bölgesel bir yapılandırması durumunda yapılandırılan önbellek ile hangi API Management bölgesel dağıtımın iletişim kuracağını belirtir. **Varsayılan** olarak belirtilen önbellekler, bölgesel bir değere sahip önbellekler tarafından geçersiz kılınır.
>
> Örneğin, API Management Doğu ABD, Güneydoğu Asya ve Batı Avrupa bölgelerinde barındırılıyorsa ve iki önbellek yapılandırılırsa, biri **varsayılan** diğeri ve **Güneydoğu Asya**için bir tane yapılandırılmışsa, **Güneydoğu** Asya 'daki API Management kendi önbelleğini kullanır, ancak diğer iki bölge **varsayılan** önbellek girişini kullanacaktır.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Aynı aboneliğden Redsıs için Azure önbelleği ekleme

1. Azure portal API Management örneğinizi inceleyin.
2. Sol taraftaki menüden **dış önbellek** sekmesini seçin.
3. **+ Ekle** düğmesine tıklayın.
4. **Önbellek örneği** açılan alanında önbelleğinizi seçin.
5. **Varsayılan** ' ı seçin veya istediğiniz bölgeyi açılan **menüden kullan** alanından belirtin.
6. **Kaydet**’e tıklayın.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Geçerli Azure aboneliğinin veya genel olarak Azure 'un dışında barındırılan Red, için Azure önbelleği ekleme

1. Azure portal API Management örneğinizi inceleyin.
2. Sol taraftaki menüden **dış önbellek** sekmesini seçin.
3. **+ Ekle** düğmesine tıklayın.
4. **Önbellek örneği** açılan alanında **özel** ' i seçin.
5. **Varsayılan** ' ı seçin veya istediğiniz bölgeyi açılan **menüden kullan** alanından belirtin.
6. **Bağlantı dizesi** alanında redsıs bağlantı dizesi Için Azure önbelleğinizi sağlayın.
7. **Kaydet**’e tıklayın.

## <a name="use-the-external-cache"></a>Dış önbelleği kullanma

Azure API Management 'de dış önbellek yapılandırıldıktan sonra, önbelleğe alma ilkeleri aracılığıyla kullanılabilir. Ayrıntılı adımlar için bkz. [Azure API Management performansı artırmak için önbelleğe alma ekleme](api-management-howto-cache.md) .

## <a name="next-steps"> </a>Sonraki adımlar

* Önbelleğe alma ilkeleri hakkında daha fazla bilgi için bkz. [API Management ilke başvurusu][API Management policy reference]’nda [Önbelleğe alma ilkeleri][Caching policies].
* Anahtar kullanım ilkesi ifadeleri hakkında daha fazla bilgi için bkz. [Azure API Management’te özel önbelleğe alma](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

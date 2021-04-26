---
title: Sanal ağda Azure Spring Cloud sorunlarını giderme
description: Azure yay bulutu sanal ağı için sorun giderme kılavuzu.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: a84ed5a6f80b038ced2077fe3c038bd9801ca033
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732950"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Sanal ağlarda Azure Spring Cloud sorunlarını giderme

Bu belge, sanal ağlarda Azure yay bulutu kullanılırken oluşabilecek çeşitli sorunları çözmenize yardımcı olur.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure yay bulut hizmeti örneği oluştururken bir sorunla karşılaştım

Azure Spring Cloud 'ın bir örneğini oluşturmak için, örneği sanal ağa dağıtmak için yeterli izne sahip olmanız gerekir.  Spring Cloud Service örneğinin kendisi [, sanal ağa Azure Spring Cloud Service Izni vermelidir](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Azure yay bulut hizmeti örneğini kurmak için Azure portal kullanıyorsanız, Azure portal izinleri doğrular.

Azure [CLI](/cli/azure/get-started-with-azure-cli)'Yı kullanarak Azure yay bulut hizmeti örneğini ayarlamak için şunları doğrulayın:

- Abonelik etkin.
- Konum, Azure Spring Cloud tarafından desteklenir.
- Örnek için kaynak grubu zaten oluşturulmuş.
- Kaynak adı adlandırma kuralına uyar. Yalnızca küçük harf, sayı ve kısa çizgi içermelidir. İlk karakter harf olmalıdır. Son karakter harf veya sayı olmalıdır. Değer, 2 ile 32 karakter arasında olmalıdır.

Azure Spring Cloud Service örneğini Kaynak Yöneticisi şablonunu kullanarak ayarlamak için, [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlama](../azure-resource-manager/templates/template-syntax.md)bölümüne bakın.

### <a name="common-creation-issues"></a>Yaygın oluşturma sorunları

| Hata İletisi | Nasıl düzeltilir |
|------|------|
| Azure Spring Cloud tarafından oluşturulan kaynaklara ilke tarafından izin verilmedi. | Ağ kaynakları, Azure Spring Cloud 'ı kendi sanal ağınıza dağıtırken oluşturulacaktır. Lütfen bu oluşturmayı engellemek için [Azure ilkesinin](../governance/policy/overview.md) tanımlanıp tanımlanmadığını denetleyin. Oluşturulacak kaynaklar hata iletisinde bulunabilir. |
| Gerekli trafik allowlistelenmedi. | Gerekli trafiğin allowlistelendiğinden emin olmak için lütfen [VNET 'Te Azure Spring Cloud 'ı çalıştırmaya yönelik müşteri sorumluluklarına](spring-cloud-vnet-customer-responsibilities.md) bakın. |

## <a name="my-application-cant-be-registered"></a>Uygulamam kaydedilemiyor

Bu sorun, sanal ağınız özel DNS ayarlarıyla yapılandırılmışsa oluşur. Bu durumda, Azure Spring Cloud tarafından kullanılan özel DNS bölgesi verimsiz değildir. IP 168.63.129.16 Azure DNS özel DNS sunucusuna yukarı akış DNS sunucusu olarak ekleyin.

## <a name="other-issues"></a>Diğer sorunlar

[Yaygın Azure yay bulutu sorunlarını giderin](./spring-cloud-troubleshoot.md).
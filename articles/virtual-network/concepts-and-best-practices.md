---
title: Azure sanal ağ-kavramlar ve en iyi uygulamalar
description: Azure sanal ağ kavramları ve en iyi uygulamalar hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223627"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure sanal ağ kavramları ve en iyi uygulamalar

Bu makalede, Azure sanal ağ (VNet) için temel kavramlar ve en iyi uygulamalar açıklanmaktadır.

## <a name="vnet-concepts"></a>VNet kavramları

- **Adres alanı:** VNet oluştururken, genel ve özel (RFC 1918) adreslerini kullanarak özel bir özel IP adresi alanı belirtmeniz gerekir. Azure, bir sanal ağdaki kaynaklara, atadığınız adres alanından özel bir IP adresi atar. Örneğin, adres alanı 10.0.0.0/16 olan VNet 'te bir VM dağıtırsanız, VM 'de 10.0.0.4 gibi özel bir IP atanır.
- **Alt ağlar:** Alt ağlar, sanal ağı bir veya daha fazla alt ağa segmentetmenize ve sanal ağın adres alanının bir bölümünü her alt ağa ayırmayı sağlar. Ardından, belirli bir alt ağda Azure kaynaklarını dağıtabilirsiniz. Geleneksel ağlarda olduğu gibi, alt ağlar, VNet adres alanınızı kuruluşun iç ağı için uygun parçalara ayırmanıza olanak tanır. Bu ayrıca adres ayırma verimliliğini de artırır. Ağ güvenlik gruplarını kullanarak alt ağlar içindeki kaynakların güvenliğini sağlayabilirsiniz. Daha fazla bilgi için bkz. [ağ güvenlik grupları](./network-security-groups-overview.md).
- **Bölgeler**: VNET tek bir bölgeye/konuma göre kapsamlandırılır; Ancak, farklı bölgelerdeki birden fazla sanal ağ, sanal ağ eşlemesi kullanılarak birbirine bağlanabilir.
- **Abonelik:** VNet, bir aboneliğe göre kapsamlandırılır. Her Azure [aboneliğinde](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ve Azure [bölgesinde](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) birden çok sanal ağ uygulayabilirsiniz.

## <a name="best-practices"></a>En iyi uygulamalar

Ağınızı Azure 'da oluştururken, aşağıdaki evrensel tasarım ilkelerini göz önünde bulundurmanız önemlidir:

- Çakışmayan adres alanlarının bulunduğundan emin olun. VNet adres alanının (CıDR bloğu) kuruluşunuzun diğer ağ aralıklarıyla çakışmadığından emin olun.
- Alt ağlarınız VNet 'in tüm adres alanını kapsamamalıdır. Daha önce planlayın ve daha sonra adres alanı ayırın.
- Birden çok küçük VNET yerine daha az büyük sanal ağ kullanmanız önerilir. Bu, yönetim yükünü engeller.
- Ağ güvenlik gruplarını (NSG 'ler) bunların altındaki alt ağlara atayarak VNet 'iniz için güvenli hale getirin.

## <a name="next-steps"></a>Sonraki adımlar

 Bir sanal ağı kullanmaya başlamak için bir sanal ağ oluşturun, bu sanal ağa birkaç sanal makine dağıtın ve sanal makineler arasında iletişim kurun. Nasıl olduğunu öğrenmek için [Sanal ağ oluşturma](quick-create-portal.md) başlıklı hızlı başlangıca bakın.
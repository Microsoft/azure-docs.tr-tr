---
title: Azure DNS özel bölgelerin oto kaydı özelliği nedir?
description: Azure DNS özel bölgelerin oto kayıt özelliğine genel bakış
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 2e5f41c0e149c99b5524c439c59e72afe554c776
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783906"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS özel bölgelerin oto kaydı özelliği nedir?

Azure DNS özel bölgeler otomatik kayıt özelliği, bir sanal ağda dağıtılan sanal makineler için DNS kayıt yönetiminin ortadan çıkar. [Bir sanal ağı](./private-dns-virtual-network-links.md) özel bir DNS bölgesi ile bağladığınızda ve tüm sanal makineler için otomatik kaydı etkinleştirdiğinizde, sanal ağda dağıtılan sanal makinelerin DNS KAYıTLARı özel DNS bölgesinde otomatik olarak oluşturulur. İleriye doğru arama kayıtlarının (A kayıtları) yanı sıra, sanal makineler için de otomatik olarak kayıt (PTR kayıtları) oluşturulur.
Sanal ağa daha fazla sanal makine eklerseniz, bu sanal makineler için DNS kayıtları Ayrıca bağlantılı özel DNS bölgesinde otomatik olarak oluşturulur.

Bir sanal makineyi sildiğinizde, sanal makine için DNS kayıtları otomatik olarak özel DNS bölgesinden silinir.

Bir sanal ağ bağlantısı oluştururken "otomatik kaydolmayı etkinleştir" seçeneğini belirleyerek otomatik kaydı etkinleştirebilirsiniz.

![Otomatik kaydı etkinleştir](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Kısıtlamalar

* Oto kayıt yalnızca sanal makineler için geçerlidir. İç yük dengeleyiciler gibi diğer tüm kaynaklar için, DNS kayıtlarını sanal ağa bağlı özel DNS bölgesinde el ile oluşturabilirsiniz.
* DNS kayıtları yalnızca birincil sanal makine NIC 'ı için otomatik olarak oluşturulur. Sanal makinelerinizde birden fazla NIC varsa, diğer ağ arabirimlerinin DNS kayıtlarını el ile oluşturabilirsiniz.
* DNS kayıtları yalnızca birincil sanal makine NIC 'ı DHCP kullanıyorsa otomatik olarak oluşturulur. Statik IP adresleri yapılandırılırsa (örneğin, [Azure 'da birden çok IP adresi](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)kullanmak için), otomatik kayıt bu sanal makine için kayıt oluşturmaz.
* IPv6 (AAAA kayıtları) için oto kaydı desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure PowerShell](./private-dns-getstarted-powershell.md) veya [Azure CLI](./private-dns-getstarted-cli.md)kullanarak Azure DNS bir özel bölge oluşturmayı öğrenin.

* Azure DNS özel bölgelerle gerçekleştirilen bazı yaygın [özel bölge senaryoları](./private-dns-scenarios.md) hakkında bilgi edinin.

* Belirli işlemler için bekleneceğiniz belirli davranış dahil olmak üzere Azure DNS özel bölgeler hakkında sık sorulan sorular ve yanıtlar için, bkz. [özel DNS SSS](./dns-faq-private.md).

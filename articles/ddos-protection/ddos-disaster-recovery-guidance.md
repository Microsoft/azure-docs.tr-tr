---
title: Azure DDoS koruması standart iş sürekliliği | Microsoft Docs
description: Azure DDoS koruma standardı 'nı etkileyen bir Azure hizmeti kesintisi durumunda yapmanız gerekenler hakkında bilgi edinin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730497"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS koruması standart – iş sürekliliği

Azure DDoS koruma standardı 'nda iş sürekliliği ve olağanüstü durum kurtarma, işletmenizin kesintiye uğramadan çalışmaya devam etmesini sağlar. Bu makalede kullanılabilirlik (bölge içi) ve olağanüstü durum kurtarma ele alınmaktadır.

## <a name="overview"></a>Genel Bakış
Azure DDoS Koruması Standadı sanal ağlardaki genel IP adreslerini korur. Korumanın herhangi bir yeni veya mevcut sanal ağda etkinleştirilmesi basittir ve herhangi bir uygulama veya kaynak değişikliği gerektirmez.

Bir sanal ağ (VNet), buluttaki ağınızın mantıksal bir gösterimidir. Sanal ağlar Azure Application Gateway, Azure Güvenlik Duvarı ve Azure sanal makineleri gibi kaynaklarınızı barındırmak için bir güven sınırı olarak hizmet verir. Bir bölgenin kapsamı içinde oluşturulur. İki farklı bölgede (örneğin, ABD Doğu ve ABD Batı) aynı adres alanı ile sanal ağlar *oluşturabilirsiniz* , ancak aynı adres alanına sahip olduklarından bunları birbirine bağlanamazsınız. 

## <a name="business-continuity"></a>İş sürekliliği

Uygulamanızın kesintiye uğramaması için birkaç farklı yol olabilir. Bir bölge, doğal bir olağanüstü durum nedeniyle tamamen kesilebilir veya çok sayıda cihaz veya hizmetten kaynaklanan bir hatadan dolayı kısmi bir olağanüstü durum olabilir. Korumalı sanal ağlarınızdaki etki bu durumların her birinde farklılık açmış.

**S: tüm bölge için bir kesinti oluşursa, ne yapmalıyım? Örneğin, bir bölge doğal bir olağanüstü durum nedeniyle tamamen kesiliyorsa? Bölgede barındırılan sanal ağlara ne olur?**

Y: sanal ağ ve etkilenen bölgedeki kaynaklar, hizmet kesintisi süresi boyunca erişilemez durumda kalır.

![Basit sanal ağ diyagramı.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**S: farklı bir bölgede aynı sanal ağı yeniden oluşturmayı ne yapabilirim?**

Y: sanal ağlar oldukça hafif kaynaklardır. Farklı bir bölgede aynı adres alanına sahip bir VNet oluşturmak için Azure API 'Lerini çağırabilirsiniz. Etkilenen bölgede mevcut ortamı yeniden oluşturmak için, sahip olduğunuz VNET 'lerdeki kaynakları yeniden dağıtmak üzere API çağrıları yaparsınız. Karma dağıtımda olduğu gibi şirket içi bağlantınız varsa, yeni bir VPN Gateway dağıtmanız ve şirket içi ağınıza bağlanmanız gerekir.

Bir sanal ağ oluşturmak için bkz. [sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

**S: belirli bir bölgedeki VNet 'in çoğaltması, başka bir bölgede daha önce yeniden oluşturulabilir mi?**

Y: Evet, aynı özel IP adresi alanını ve kaynakları daha önce iki farklı bölgede kullanarak iki sanal ağ oluşturabilirsiniz. VNet 'te internet 'e yönelik hizmetler barındırıyorsanız, etkin olan bölgeye coğrafi yönlendirme trafiği Traffic Manager ayarlamış olabilirsiniz. Ancak, yönlendirme sorunlarına yol açacağından, aynı adres alanına sahip iki VNET 'i şirket içi ağınıza bağlanamazsınız. Tek bir bölgedeki bir sanal ağın olağanüstü durum ve kayıp durumunda, kullanılabilir bölgedeki diğer VNet 'i şirket içi ağınıza eşleşen adres alanı ile bağlayabilirsiniz.

Bir sanal ağ oluşturmak için bkz. [sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.
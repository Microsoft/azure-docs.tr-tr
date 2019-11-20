---
title: Sanal ağ iş sürekliliği | Microsoft Docs
description: Azure sanal ağlarını etkileyen bir Azure hizmeti kesintisi durumunda ne yapılacağını öğrenin.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876182"
---
# <a name="virtual-network--business-continuity"></a>Sanal ağ – Iş sürekliliği

## <a name="overview"></a>Genel Bakış
Bir sanal ağ (VNet), buluttaki ağınızın mantıksal bir gösterimidir. Kendi özel IP adresi alanınızı tanımlamanızı ve ağı alt ağlara bölüetmenize olanak tanır. VNET 'ler, Azure sanal makineleri ve Cloud Services (Web/çalışan rolleri) gibi işlem kaynaklarınızı barındırmak için bir güven sınırı görevi görür. VNet, içinde barındırılan kaynaklar arasında doğrudan özel IP iletişimine izin verir. Bir VPN Gateway veya ExpressRoute aracılığıyla bir sanal ağı şirket içi ağa bağlayabilirsiniz.

Bir sanal ağ, bir bölgenin kapsamı içinde oluşturulur. İki farklı bölgede (örneğin, ABD Doğu ve ABD Batı) aynı adres alanı ile sanal ağlar *oluşturabilirsiniz* , ancak aynı adres alanına sahip olduklarından bunları birbirine bağlanamazsınız. 

## <a name="business-continuity"></a>İş Sürekliliği

Uygulamanızın kesintiye uğramaması için birkaç farklı yol olabilir. Bir bölge, doğal bir olağanüstü durum nedeniyle tamamen kesilebilir veya çok sayıda cihaz veya hizmetten kaynaklanan bir hatadan dolayı kısmi bir olağanüstü durum olabilir. VNet hizmetindeki etki bu durumların her birinde farklıdır.

**S: Tüm bölge için bir kesinti oluşursa, ne yapmalıyım? Örneğin, bir bölge doğal bir olağanüstü durum nedeniyle tamamen kesiliyorsa? Bölgede barındırılan sanal ağlara ne olur?**

Y: Sanal ağ ve etkilenen bölgedeki kaynaklar, hizmet kesintisi süresi boyunca erişilemez durumda kalır.

![Basit sanal ağ diyagramı](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**S: Aynı sanal ağı farklı bir bölgede yeniden oluşturmayı ne yapabilirim?**

Y: Sanal ağlar oldukça hafif kaynaklardır. Farklı bir bölgede aynı adres alanına sahip bir VNet oluşturmak için Azure API 'Lerini çağırabilirsiniz. Etkilenen bölgede bulunan aynı ortamı yeniden oluşturmak için, Cloud Services Web ve çalışan rollerini ve sahip olduğunuz sanal makineleri yeniden dağıtmak üzere API çağrıları yaparsınız. Karma dağıtımda olduğu gibi şirket içi bağlantınız varsa, yeni bir VPN Gateway dağıtmanız ve şirket içi ağınıza bağlanmanız gerekir.

Bir sanal ağ oluşturmak için bkz. [sanal ağ oluşturma](manage-virtual-network.md#create-a-virtual-network).

**S: Belirli bir bölgedeki VNet 'in bir çoğaltması, başka bir bölgede daha önce yeniden oluşturulabilir mi?**

Y: Evet, aynı özel IP adresi alanını ve kaynakları daha önce iki farklı bölgede kullanarak iki sanal ağ oluşturabilirsiniz. VNet 'te internet 'e yönelik hizmetler barındırıyorsanız, etkin olan bölgeye coğrafi yönlendirme trafiği Traffic Manager ayarlamış olabilirsiniz. Ancak, yönlendirme sorunlarına yol açacağından, aynı adres alanına sahip iki VNET 'i şirket içi ağınıza bağlanamazsınız. Tek bir bölgedeki bir sanal ağın olağanüstü durum ve kayıp durumunda, kullanılabilir bölgedeki diğer VNet 'i şirket içi ağınıza eşleşen adres alanı ile bağlayabilirsiniz.

Bir sanal ağ oluşturmak için bkz. [sanal ağ oluşturma](manage-virtual-network.md#create-a-virtual-network).


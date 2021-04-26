---
title: Azure Stack Edge sanal özel ağ (VPN) üzerinde iş sürekliliği ve olağanüstü durum kurtarmayı (BCDR) yapılandırma
description: Azure Stack Edge VPN üzerinde BCDR 'nin nasıl yapılandırılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367699"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Azure Stack Edge VPN için iş sürekliliği ve olağanüstü durum kurtarmayı yapılandırma

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Bu makalede, Azure Stack Edge cihazında yapılandırılmış bir sanal özel ağ (VPN) üzerinde iş sürekliliği ve olağanüstü durum kurtarma (BCDR) yapılandırma açıklanmaktadır.

Bu makale hem Azure Stack Edge Pro R hem de Azure Stack Edge Mini R cihazı için geçerlidir.

## <a name="configure-failover-to-a-paired-region"></a>Bir eşleştirilmiş bölgeye yük devretmeyi yapılandırma

Azure Stack Edge cihazınız, Azure depolama gibi diğer Azure hizmetlerini kullanır. Azure Stack Edge cihazı tarafından kullanılan belirli bir Azure hizmetinde BCDR 'yi yapılandırabilirsiniz. Azure Stack Edge tarafından kullanılan bir Azure hizmeti, eşleştirilmiş bölgesine devredilirse, Azure Stack Edge cihazı artık yeni IP adreslerine bağlanır ve iletişim, şüpheli olarak şifrelenmez. 

Azure Stack Edge cihazı, bölünmüş tünelleme ve giriş bölgesinde yapılandırılan tüm veri ve Hizmetleri (Azure Stack Edge cihazlarınız ile ilişkili bölge) VPN tünelinden devralarak kullanır. Azure Hizmetleri, giriş bölgesinin dışında bir eşleştirilmiş bölgeye yük devreder, daha sonra veriler artık VPN üzerinden gitmeyecektir ve bu nedenle buna düzgün şekilde şifrelenmez. 

Bu senaryoda, genellikle yalnızca el ile Azure hizmetleri etkilenir. Bu sorunu gidermek için, Azure Stack Edge VPN yapılandırmasında aşağıdaki değişiklikler yapılmalıdır:

1. Azure Stack Edge üzerinde VPN için kapsamlı yollara yük devretme Azure hizmet IP aralıklarını ekleyin. Hizmetler daha sonra VPN üzerinden yönlendirilme başlar.

    Kapsamlı yolları eklemek için, hizmete özgü yollara sahip json dosyasını indirmeniz gerekir. Bu dosyayı yeni yollarla güncelleştirdiğinizden emin olun.
2. Azure yol tablosuna karşılık gelen Azure hizmet IP aralıklarını ekleyin.
3. Yolları güvenlik duvarına ekleyin.

> [!NOTE]
>
> 1. Bir Azure VPN ağ geçidi ve Azure sanal ağı (VNET) yük devretmesi, [olağanüstü durum nedeniyle başarısız olan bir Azure bölgesinden kurtarma](#recover-from-a-failed-azure-region)bölümünde karşılanır.
> 2. Azure yol tablosuna eklenen IP aralıkları 400 sınırını geçemez. Bu durumda, [bir Azure bölgesinden başka bir Azure bölgesine geçiş](#move-from-an-azure-region-to-another)yapmak için bölümündeki yönergeleri izlemeniz gerekir.

## <a name="recover-from-a-failed-azure-region"></a>Başarısız bir Azure bölgesinden kurtarma

Tüm Azure bölgesinin deprem gibi çok zararlı bir olay nedeniyle yük devredemediği durumda, Azure Stack Edge hizmeti de dahil olmak üzere bu bölgedeki tüm Azure hizmetlerinin yük devri yapılır. Birden çok hizmet olduğundan, kapsamlı yollar kolayca birkaç yüzlerce olarak değişebilir. Azure 'da 400 yol sınırlaması vardır. 

Bölge devredildiği zaman, sanal ağ (VNet) de yeni bölgeye devredilmez ve bu nedenle sanal ağ geçidi (VPN ağ geçidi) olur. Bu değişikliği çözmek için Azure Stack Edge VPN yapılandırmanızda aşağıdaki değişiklikleri yapın:

1. VNET ' i hedef bölgeye taşıyın. Daha fazla bilgi için bkz. [Azure Portal aracılığıyla bir Azure sanal ağını başka bir bölgeye taşıma](../virtual-network/move-across-regions-vnet-portal.md).
2. Sanal ağı taşıdığınız hedef bölgede yeni bir Azure VPN ağ geçidi dağıtın. Daha fazla bilgi için bkz. [sanal ağ geçidi oluşturma](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Azure Stack Edge VPN yapılandırmasını VPN bağlantısında yukarıdaki VPN ağ geçidini kullanacak şekilde güncelleştirin ve ardından VPN ağ geçidini kullanan yönlendirmeler eklemek için hedef bölgeyi seçin.
4. İstemci adres havuzu da değişirse gelen Azure yol tablosunu güncelleştirin. 

## <a name="move-from-an-azure-region-to-another"></a>Bir Azure bölgesinden diğerine geçme

Azure Stack Edge cihazınızı bir konumdan başka bir konuma taşıyabilirsiniz. Cihazınızın dağıtıldığı yere en yakın bölgeyi kullanmak için, cihazı yeni bir giriş bölgesi için yapılandırmanız gerekir. Aşağıdaki değişiklikleri yapın:

1. Azure Stack Edge VPN yapılandırmasını yeni bir bölgenin VPN ağ geçidini kullanacak şekilde güncelleştirebilir ve VPN Gateway kullanan yollar eklemek için yeni bölgeyi seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge cihazınızı yedekleyin](azure-stack-edge-gpu-prepare-device-failure.md).
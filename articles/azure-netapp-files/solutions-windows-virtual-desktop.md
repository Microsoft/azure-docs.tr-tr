---
title: Windows sanal masaüstü Azure NetApp Files kullanma | Microsoft Docs
description: Azure NetApp Files ile Windows sanal masaüstü dağıtımı için en iyi uygulama kılavuzunuzu ve örnek şemaları sağlar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a765d689307b7f56e5100e75d9f7121e944cea14
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168289"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Windows Sanal Masaüstü ile Azure NetApp Files kullanmanın avantajları 

Bu makalede, Azure NetApp Files ile Windows sanal masaüstü (WVD) dağıtımı hakkında en iyi Yöntem Kılavuzu sunulmaktadır.

Azure NetApp Files, Azure 'un yüksek performanslı bir dosya depolama hizmetidir. Windows sanal masaüstü dağıtımlarının son derece büyük ölçekli ölçeğini destekleyebilen en çok 450.000 ıOPS ve alt milisaniyelik gecikme süresi sağlayabilir. Veri düzlemi erişimini korurken, g/ç 'yi duraklatmadan Azure NetApp Files hacimlerinizin hizmet düzeyini isteğe bağlı olarak değiştirebilir ve neredeyse anında ayarlayabilirsiniz. Bu özellik, WVD Deployment ölçeğini maliyet açısından kolayca iyileştirmenize olanak tanır. Ayrıca, birim performansını etkilemeden, verimli, zaman içinde ses anlık görüntüleri oluşturabilirsiniz. Bu özellik, tek bir [Fslogix Kullanıcı profili kapsayıcılarını](../virtual-desktop/store-fslogix-profile.md) dizinden bir kopya aracılığıyla geri almanızı `~snapshot` veya birim geri alma özelliği aracılığıyla tüm birimi bir kerede geri almayı anında mümkün kılar.  Bir birimi veri kaybından veya bozulmaya karşı korumak için en fazla 255 (rotasyonlu) anlık görüntüsü ile yöneticiler, nelerin yapıldığını geri alma şansınız vardır.

## <a name="sample-blueprints"></a>Örnek planlar

Aşağıdaki örnek planlar Windows sanal masaüstü 'nün Azure NetApp Files tümleştirmesini göstermektedir. Havuza alınmış bir masaüstü senaryosunda, kullanıcılar [Çoklu oturum sanal makinelerini](../virtual-desktop/windows-10-multisession-faq.yml#what-is-windows-10-enterprise-multi-session)kullanarak, havuzdaki en iyi kullanılabilir oturuma (en [ilk mod](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) ana bilgisayar olarak yönlendirilir. Diğer yandan, kişisel masaüstleri her bir kullanıcının kendi sanal makinesine sahip olduğu senaryolar için ayrılmıştır.

### <a name="pooled-desktop-scenario"></a>Havuza alınmış masaüstü senaryosu

Havuza alınmış senaryo için, Windows sanal masaüstü ekibi, kullanıcı sayısına vCPU 'ya göre aşağıdaki kılavuzu [önerir](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) . Bu öneriye göre hiçbir sanal makine boyutu belirtilmediğini unutmayın.

|     İş yükü türü     |     Açık    |     Orta    |     Ağır    |
|-----------------------|--------------|---------------|--------------|
|     VCPU başına Kullanıcı sayısı    |     6        |     4         |     2        |


Bu öneri, her bir D16as_V4 sanal makinesinde yaklaşık 62 "bilgi/orta Kullanıcı" olarak günlüğe kaydederek 500-Kullanıcı Logınsı testi tarafından onaylanır. 

Örnek olarak, D16as_V4 sanal makine başına 62 Kullanıcı Azure NetApp Files, her ortam için 60.000 kullanıcıyı kolayca destekleyebilir. D32as_v4 sanal makinenin üst limitini değerlendirmek için test işlemi devam etmektedir. VCPU başına WVD User önerisi D32as_v4, aşağıdaki şekilde gösterildiği gibi, 120.000 ' den fazla kullanıcının [1.000 IP VNET limitini](./azure-netapp-files-network-topologies.md)broaching önce 1.000 sanal makinelere sığmasını sağlamak için geçerlidir.  

![Windows sanal masaüstü havuza alınmış masaüstü senaryosu](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Kişisel masaüstü senaryosu 

Bir kişisel masaüstü senaryosunda aşağıdaki şekilde genel amaçlı mimari önerisi gösterilmektedir. Kullanıcılar, belirli masaüstü yığınlarını eşleştirmekte ve her Pod, 1.000 sanal makineye sahiptir ve yönetim VNet 'ten IP adresleri yaymaya yer bırakır. Azure NetApp Files, tek oturum ana bilgisayar havuzu başına 900 + kişisel masaüstlerini kolayca işleyebilir. Bu, gerçek sayıda sanal makine, hub VNet 'te bulunan yönetim ana bilgisayarlarının sayısına eşit olarak 1.000. Daha fazla kişisel masaüstü gerekiyorsa, aşağıdaki şekilde gösterildiği gibi daha fazla Pod (konak havuzları ve sanal ağ) eklemek çok kolaydır. 

![Windows sanal masaüstü kişisel masaüstü senaryosu](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Bu gibi bir POD tabanlı mimari oluştururken, oturum açma sırasında kullanıcıları doğru Pod 'a atamak, kullanıcıların her zaman kullanıcı profillerini bulabilmeleri için önem derecesine sahip olur. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure NetApp Files kullanan çözüm mimarileri](azure-netapp-files-solution-architectures.md)
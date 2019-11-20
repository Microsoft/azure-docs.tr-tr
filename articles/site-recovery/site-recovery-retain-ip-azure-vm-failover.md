---
title: Azure Site Recovery ile Azure VM yük devretmesinin ardından IP adreslerini tut
description: Azure Site Recovery ile ikincil bir bölgeye olağanüstü durum kurtarma için Azure VM 'lerinden yük devrettikten sonra IP adreslerinin nasıl tutulacağını açıklar
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083684"
---
# <a name="retain-ip-addresses-during-failover"></a>Yük devretme sırasında IP adreslerini koruma

[Azure Site Recovery](site-recovery-overview.md) , VM 'leri başka bir Azure bölgesine çoğaltarak, bir kesinti oluşursa yük devreder ve nesneler normal 'e geri döndüğünüzde birincil bölgeye geri dönebilir.

Yük devretme sırasında, IP adreslemesini kaynak bölgeyle özdeş olan hedef bölgede tutmak isteyebilirsiniz:

- Varsayılan olarak, Azure VM 'Leri için olağanüstü durum kurtarmayı etkinleştirdiğinizde Site Recovery kaynak kaynak ayarlarına dayalı olarak hedef kaynakları oluşturur. Statik IP adresleriyle yapılandırılmış Azure VM 'Leri için Site Recovery, kullanımda değilse hedef VM için aynı IP adresini sağlamaya çalışır. Site Recovery adresleme 'nin nasıl ele aldığını gösteren tam bir açıklama için [Bu makaleyi gözden geçirin](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Basit uygulamalar için varsayılan yapılandırma yeterlidir. Daha karmaşık uygulamalar için bağlantının yük devretmeden sonra beklendiği gibi çalıştığından emin olmak için ek kaynak sağlamanız gerekebilir.


Bu makalede, daha karmaşık örnek senaryolarda IP adreslerini saklamak için bazı örnekler sağlanmaktadır. Örnekler şunlardır:

- Azure 'da çalışan tüm kaynaklarla şirket için yük devretme
- Karma dağıtıma sahip bir şirket için yük devretme ve hem şirket içinde hem de Azure 'da çalışan kaynaklar

## <a name="resources-in-azure-full-failover"></a>Azure 'daki kaynaklar: tam yük devretme

Şirket A 'nın Azure 'da çalışan tüm uygulamaları vardır.

### <a name="before-failover"></a>Yük devretmeden önce

Yük devretmeden önce bu mimari aşağıda verilmiştir.

- Şirket A 'nın kaynak ve hedef Azure bölgelerinde aynı ağları ve alt ağları vardır.
- Kurtarma süresi hedefini (RTO) azaltmak için, şirket SQL Server her zaman açık, etki alanı denetleyicileri vb. için çoğaltma düğümleri kullanır. Bu çoğaltma düğümleri hedef bölgedeki farklı bir VNet içinde bulunur, böylece kaynak ve hedef bölgeler arasında VPN siteden siteye bağlantı kurabilir. Kaynak ve hedefte aynı IP adresi alanı kullanılıyorsa bu mümkün değildir.  
- Yük devretmeden önce, ağ mimarisi aşağıdaki gibidir:
    - Birincil bölge Azure Doğu Asya
        - Doğu Asya, 10.1.0.0/16 adres alanı ile VNet 'e (**kaynak VNET**) sahiptir.
        - Doğu Asya VNet 'teki üç alt ağa bölünen iş yükleri vardır:
            - **Alt ağ 1**: 10.1.1.0/24
            - **Alt ağ 2**: 10.1.2.0/24
            - **Alt ağ 3**: 10.1.3.0/24
    - İkincil (hedef) bölge Azure Güneydoğu Asya
        - Güneydoğu Asya 'nın, **kaynak VNET**ile özdeş bir kurtarma VNET (**Kurtarma VNET**) vardır.
        - Güneydoğu Asya 'da 10.2.0.0/16 adres alanı ile ek bir VNet (**Azure VNET**) vardır.
        - **Azure VNET** , adres alanı 10.2.4.0/24 olan bir alt ağ (**alt ağ 4**) içerir.
        - SQL Server her zaman açık, etki alanı denetleyicisi vb. için çoğaltma düğümleri **alt ağ 4**' te bulunur.
    - **Kaynak VNET** ve **Azure VNET** , bir VPN siteden siteye bağlantısı ile bağlanır.
    - **Kurtarma VNET** başka bir sanal ağla bağlantılı değil.
    - **Şirket A** çoğaltılan öğeler IÇIN hedef IP adreslerini atar/doğrular. Hedef IP her VM için kaynak IP ile aynıdır.

![Tam yük devretmeden önce Azure 'daki kaynaklar](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Yük devretmeden sonra

Kaynak bölgesel bir kesinti oluşursa, A şirketi tüm kaynaklarını hedef bölgeye devreder.

- Yük devretme işleminden önce hedef IP adresleriyle zaten mevcut olan Şirket A, yük devretmeyi düzenleyebilir ve **Kurtarma VNET** Ile **Azure VNET**arasında yük devretmeden sonra otomatik olarak bağlantı kurabilir. Bu, aşağıdaki diyagramda gösterilmiştir.
- Uygulama gereksinimlerine bağlı olarak, hedef bölgedeki iki sanal ağ (**Kurtarma VNET** ve **Azure VNET**) arasındaki bağlantılar, (ara adım olarak) veya yük devretmeden önce oluşturulabilir.
  - Şirket, bağlantıların ne zaman kurulacağıdır belirtmek için [Kurtarma planlarını](site-recovery-create-recovery-plans.md) kullanabilir.
  - VNet eşlemesi veya siteden siteye VPN kullanan sanal ağlar arasında bağlantı kuramazlar.
      - VNet eşlemesi bir VPN ağ geçidi kullanmaz ve farklı kısıtlamaları vardır.
      - VNet eşleme [fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network) , VNet-VNET VPN Gateway [fiyatlandırmadan](https://azure.microsoft.com/pricing/details/vpn-gateway)farklı şekilde hesaplanır. Yük devretme işlemleri için, genellikle öngörülemeyen ağ olaylarını en aza indirmek için bağlantı türü de dahil olmak üzere kaynak ağlarla aynı bağlantı yöntemini kullanmayı tavsiye ederiz.

    ![Azure 'da tam yük devretme kaynakları](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Azure 'daki kaynaklar: yalıtılmış uygulama yük devretmesi

Uygulama düzeyinde yük devri yapmanız gerekebilir. Örneğin, belirli bir uygulama veya adanmış bir alt ağda bulunan uygulama katmanının yükünü devretmek için.

- Bu senaryoda, IP adreslemesini koruyabilir, ancak bağlantı tutarsızlıklarının olasılığını artırdığından bu durum genellikle önerilmez. Ayrıca aynı Azure sanal ağı içindeki diğer alt ağlara alt ağ bağlantısını kaybedersiniz.
- Alt ağ düzeyinde uygulama yük devretmesini yapmanın daha iyi bir yolu, yük devretme için farklı hedef IP adresleri kullanmaktır (kaynak VNet 'teki diğer alt ağlara bağlantınız olması gerekiyorsa) veya her uygulamayı kaynak bölgede kendi ayrılmış VNet 'inde yalıtabilirsiniz. İkinci yaklaşımla, kaynak bölgedeki ağlar arasında bağlantı kurabilir ve hedef bölgeye yük devretmek için aynı davranışa benzebilirsiniz.  

Bu örnekte, Şirket A, uygulamaları ayrılmış sanal ağlarda kaynak bölgeye yerleştirir ve bu sanal ağlar arasında bağlantı kurar. Bu tasarımla, yalıtılmış uygulama yük devretmesi gerçekleştirebilir ve kaynak özel IP adreslerini hedef ağda saklayabilir.

### <a name="before-failover"></a>Yük devretmeden önce

Yük devretmeden önce, mimari aşağıdaki gibidir:

- Uygulama VM 'Leri, birincil Azure Doğu Asya bölgesinde barındırılır:
    - **APP1** VM 'Ler VNet **kaynak VNET 1**: 10.1.0.0/16 konumunda bulunur.
    - **App2** VM 'Ler VNet **kaynak VNET 2**: 10.2.0.0/16 konumunda bulunur.
    - **Kaynak VNET 1** ' in iki alt ağı vardır.
    - **Kaynak VNET 2** ' nin iki alt ağı vardır.
- İkincil (hedef) bölge Azure Güneydoğu Asya-Güneydoğu Asya, **kaynak VNET 1** ve **kaynak VNET 2**ile aynı olan bir kurtarma sanal ağlarına (**Kurtarma VNET 1** ve **Kurtarma VNET 2**) sahiptir.
        - **Recovery VNET 1** ve **Kurtarma VNET 2** ' nin her biri, **kaynak VNET 1** ' deki alt ağlarla eşleşen Iki alt ağa sahiptir ve **kaynak VNET 2** -Güneydoğu Asya, adres alanı 10.3.0.0/16 olan ek bir VNET 'e (**Azure VNET**) sahiptir.
        - **Azure VNET** , adres alanı 10.3.4.0/24 olan bir alt ağ (**alt ağ 4**) içerir.
        -SQL Server her zaman açık, etki alanı denetleyicisi vb. için çoğaltma düğümleri **alt ağ 4**' te bulunur.
- Siteden siteye VPN bağlantısı sayısı vardır: 
    - **Kaynak VNET 1** ve **Azure VNET**
    - **Kaynak VNET 2** ve **Azure VNET**
    - **Kaynak VNET 1** ve **kaynak VNET 2** VPN siteden siteye bağlandı
- **Kurtarma VNET 1** ve **Kurtarma VNET 2** başka bir sanal ağa bağlı değil.
- **A şirketi** , RTO 'ı azaltmak Için **Kurtarma VNET 1** ve **Kurtarma VNET 2**' deki VPN ağ geçitlerini yapılandırır.  
- **Kurtarma VNet1** ve **Kurtarma VNet2** başka bir sanal ağla bağlantılı değildir.
- Kurtarma süresi hedefini (RTO) azaltmak için, yük devretmeden önce **Kurtarma VNet1** ve **Kurtarma VNet2** üzerinde VPN ağ geçitleri yapılandırılır.

    ![Uygulama yük devretmeden önce Azure 'daki kaynaklar](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Yük devretmeden sonra

Tek bir uygulamayı etkileyen bir kesinti veya sorun durumunda (örneğimizde * * kaynak VNet 2 ' de), Şirket A, etkilenen uygulamayı aşağıdaki şekilde kurtarabilir:


- Kaynak **VNet1** ve **kaynak VNet2**arasında ve **kaynak VNET2** ile **Azure VNET** arasında VPN bağlantılarının bağlantısını kesin.
- **Kaynak VNet1** ve **Kurtarma VNet2**arasında ve **Kurtarma VNET2** ile **Azure VNET**arasında VPN bağlantıları oluşturun.
- **Kaynak VNet2** 'de **Kurtarma VNet2**'ye yük devretme yükünü devreder.

![Azure Uygulama yük devretmesi kaynakları](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Bu örnek, daha fazla uygulama ve ağ bağlantısı içerecek şekilde genişletilebilir. Kaynak kaynaktan hedefe yük devrettikten sonra, benzer bir bağlantı modelini mümkün olduğunca güncel bir şekilde takip etmek önerilir.
- VPN ağ geçitleri, bağlantı kurmak için genel IP adreslerini ve ağ geçidi atlamalarını kullanır. Genel IP adreslerini kullanmak istemiyorsanız veya ek atlamalara engel olmak istemiyorsanız, [Azure VNET eşlemesi](../virtual-network/virtual-network-peering-overview.md) 'Ni [desteklenen Azure bölgelerinde](../virtual-network/virtual-network-manage-peering.md#cross-region)sanal ağlar arası eşleme ile kullanabilirsiniz.

## <a name="hybrid-resources-full-failover"></a>Karma kaynaklar: tam yük devretme

Bu senaryoda, **B şirketi** , Azure üzerinde çalışan uygulama altyapısının bir parçası ve şirket içi çalışan geri kalanı ile bir karma iş çalıştırır. 

### <a name="before-failover"></a>Yük devretmeden önce

Ağ mimarisinin yük devretmeden önce nasıl göründüğü aşağıda verilmiştir.

- Uygulama VM 'Leri Azure Doğu Asya içinde barındırılır.
- Doğu Asya, 10.1.0.0/16 adres alanı ile VNet 'e (**kaynak VNET**) sahiptir.
  - Doğu Asya **kaynak VNET**'te üç alt ağa bölünen iş yükleri vardır:
    - **Alt ağ 1**: 10.1.1.0/24
    - **Alt ağ 2**: 10.1.2.0/24
    - **Alt ağ 3**: 10.1.3.0/24, adres alanı 10.1.0.0/16 olan bir Azure sanal ağını kullanma. Bu sanal ağ, **kaynak VNET** olarak adlandırılmış
      - İkincil (hedef) bölge Azure Güneydoğu Asya:
  - Güneydoğu Asya 'nın, **kaynak VNET**ile özdeş bir kurtarma VNET (**Kurtarma VNET**) vardır.
- Doğu Asya sanal makineler, Azure ExpressRoute veya siteden siteye VPN ile şirket içi veri merkezine bağlanır.
- RTO 'ı azaltmak için Şirket B, yük devretmeden önce Azure Güneydoğu Asya 'daki kurtarma VNet 'teki ağ geçitlerini sağlar.
- Şirket B, çoğaltılan VM 'Ler için hedef IP adreslerini atar/doğrular. Hedef IP adresi, her VM için kaynak IP adresi ile aynıdır.


![Yük devretmeden önce Şirket içinden Azure 'a bağlantı](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Yük devretmeden sonra


Kaynak bölgesel bir kesinti oluşursa, B şirketi tüm kaynaklarını hedef bölgeye devreder.

- Yük devretme işleminden önce hedef IP adresleri zaten mevcut olduğunda, B şirketi yük devretmeyi düzenleyebilir ve **Kurtarma VNET** Ile **Azure VNET**arasında yük devretmeden sonra otomatik olarak bağlantı kurabilir.
- Uygulama gereksinimlerine bağlı olarak, hedef bölgedeki iki sanal ağ (**Kurtarma VNET** ve **Azure VNET**) arasındaki bağlantılar, (ara adım olarak) veya yük devretmeden önce oluşturulabilir. Şirket, bağlantıların ne zaman kurulacağıdır belirtmek için [Kurtarma planlarını](site-recovery-create-recovery-plans.md) kullanabilir.
- Azure Güneydoğu Asya ve şirket içi veri merkezi arasında bağlantı kurulmadan önce Azure Doğu Asya ile şirket içi veri merkezi arasındaki özgün bağlantının bağlantısı kesilmelidir.
- Şirket içi yönlendirme, hedef bölgeye işaret etmek üzere yeniden yapılandırılır ve ağ geçitleri yük devretmeye gönderilir.

![Yük devretmeden sonra Şirket içinden Azure 'a bağlantı](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Karma kaynaklar: yalıtılmış uygulama yük devretmesi

Şirket B, yalıtılmış uygulamaların yükünü alt ağ düzeyinde devreder. Bunun nedeni, kaynak ve kurtarma sanal ağları üzerindeki adres alanının aynı olması ve şirket içi bağlantının özgün kaynağı etkin olması nedeniyle oluşur.

 - Uygulama dayanıklılığı için B şirketinin, her uygulamayı kendi adanmış Azure VNet 'e yerleştirme yapması gerekir.
 - Her uygulamayla ayrı bir VNet 'te Şirket B, yalıtılmış uygulamaların yükünü devreder ve kaynak bağlantılarını hedef bölgeye yönlendirebilir.

## <a name="next-steps"></a>Sonraki adımlar

[Kurtarma planları](site-recovery-create-recovery-plans.md)hakkında bilgi edinin.

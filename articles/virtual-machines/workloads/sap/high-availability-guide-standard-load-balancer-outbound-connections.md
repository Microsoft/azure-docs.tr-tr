---
title: SAP HA senaryolarında standart ıLB&Azure VM 'Leri için genel uç nokta bağlantısı
description: SAP yüksek kullanılabilirlik senaryolarında Azure Standart Load Balancer kullanan sanal makineler için genel uç nokta bağlantısı
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 6f2268ae9a86978e9266ea0e35411727b238d4b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671643"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP yüksek kullanılabilirlik senaryolarında Azure Standart Load Balancer kullanan sanal makineler için genel uç nokta bağlantısı

Bu makalenin kapsamı, genel uç noktaları için giden bağlantıyı etkinleştiren yapılandırmaların tanımlanmasında kullanılır. Konfigürasyonlar, genellikle SUSE/RHEL için pacemaker ile yüksek kullanılabilirlik bağlamıdır.  

Yüksek kullanılabilirlik çözümünüzde Azure sınır Aracısı ile Pacemaker kullanıyorsanız, VM 'Lerin Azure Yönetim API 'sine giden bağlantısı olması gerekir. Makalesinde senaryonuz için en uygun seçeneği seçmenizi sağlayan çeşitli seçenekler sunulmaktadır.  

## <a name="overview"></a>Genel Bakış

Kümeleme aracılığıyla SAP Çözümleri için yüksek kullanılabilirlik uygularken, gerekli bileşenlerden biri [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Azure iki yük dengeleyici SKU 'su sunar: Standart ve temel.

Standart Azure yük dengeleyici, temel yük dengeleyiciye bazı avantajlar sunmaktadır. Örneğin, Azure kullanılabilirlik bölgelerinde çalışarak, daha kolay sorun giderme ve gecikme süresini azaltmak için daha iyi izleme ve günlüğe kaydetme olanakları sağlar. "HA bağlantı noktaları" özelliği tüm bağlantı noktalarını kapsamakta, diğer bir deyişle, tüm tek bağlantı noktalarını listelemek artık gerekli değildir.  

Azure Yük dengeleyicinin temel ve standart SKU 'SU arasında bazı önemli farklılıklar vardır. Bunlardan biri, giden trafiğin genel uç noktasına işlenmesiyle aynıdır. Tam temel ve standart SKU yük dengeleyici karşılaştırması için bkz. [Load Balancer SKU karşılaştırması](../../../load-balancer/load-balancer-overview.md).  
 
Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyiciye yerleştirildiğinde, ek yapılandırma yapılmadığı takdirde genel uç noktalarına giden bağlantı yok demektir.  

Bir VM 'ye genel bir IP adresi atanırsa veya VM ortak IP adresi olan bir yük dengeleyicinin arka uç havuzudur, genel uç noktalarına giden bağlantılara sahip olur.  

SAP sistemleri genellikle hassas iş verileri içerir. SAP sistemlerini barındıran VM 'Ler için genel IP adresleri aracılığıyla erişilebilmesi nadiren kabul edilebilir. Aynı zamanda, VM 'den ortak uç noktalara giden bağlantı gerektiren senaryolar da vardır.  

Azure genel uç noktasına erişim gerektiren senaryolara örnek olarak şunlar verilebilir:  
- Azure çit Aracısı, **Management.Azure.com** ve **login.microsoftonline.com** için erişim gerektirir  
- [Azure Backup](../../../backup/tutorial-backup-sap-hana-db.md#set-up-network-connectivity)
- [Azure Site Recovery](../../../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-for-urls)  
- Işletim sisteminde düzeltme eki uygulama için ortak depoyu kullanma
- SAP uygulama veri akışı, genel uç noktasına giden bağlantı gerektirebilir

SAP dağıtımınız ortak uç noktalara giden bağlantı gerektirmiyorsa, ek yapılandırmayı uygulamanız gerekmez. Yüksek kullanılabilirlik senaryonuz için iç standart SKU Azure Load Balancer oluşturmak yeterlidir, bu da genel uç noktalarından gelen bağlantı için gerekli değildir.  

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz.  
>VM 'Ler genel IP adreslerine sahip veya genel IP adresi olan Azure Yük dengeleyicisinin arka uç havuzunda zaten varsa, VM 'nin ortak uç noktalarına giden bağlantısı zaten olur.


Önce aşağıdaki kağıtları okuyun:

* Azure Standart Load Balancer
  * [Azure standart Load Balancer genel bakış](../../../load-balancer/load-balancer-overview.md) -Azure Standard yük dengeleyici, önemli ilkeler, kavramlar ve öğreticiler hakkında kapsamlı genel bakış 
  * [Azure 'Da giden bağlantılar](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) -Azure 'da giden bağlantıya ulaşmak için senaryolar
  * [Yük dengeleyici giden kuralları](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)-yük dengeleyici giden kuralları kavramlarını ve giden kuralların nasıl oluşturulacağını açıklar
* Azure Güvenlik Duvarı
  * [Azure Güvenlik duvarına genel bakış](../../../firewall/overview.md)-Azure Güvenlik Duvarı 'na genel bakış
  * [Öğretici: Azure Güvenlik Duvarı 'Nı dağıtma ve yapılandırma](../../../firewall/tutorial-firewall-deploy-portal.md) -Azure güvenlik duvarını Azure Portal aracılığıyla yapılandırma yönergeleri
* [Sanal ağlar-Kullanıcı tanımlı kurallar](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) -Azure yönlendirme kavramları ve kuralları  
* [Güvenlik grupları hizmet etiketleri](../../../virtual-network/network-security-groups-overview.md#service-tags) -hizmet etiketleriyle ağ güvenlik gruplarınızı ve güvenlik duvarı yapılandırmanızı basitleştirme

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Seçenek 1: internet 'e giden bağlantılar için ek dış Azure Standart Load Balancer

Ortak uç noktalara giden bağlantılara izin vermeden, genel uç noktalarına giden bağlantı sağlamak için bir seçenek, genel IP adresine sahip ikinci bir yük dengeleyici oluşturmak, VM 'Leri ikinci yük dengeleyicinin arka uç havuzuna eklemek ve yalnızca [giden kuralları](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)tanımlamak.  
VM 'den giden çağrılar için erişilebilen genel uç noktalarını denetlemek için [ağ güvenlik grupları](../../../virtual-network/network-security-groups-overview.md) 'nı kullanın.  
Daha fazla bilgi için belge [giden bağlantılarında](../../../load-balancer/load-balancer-outbound-connections.md#scenarios)Senaryo 2 ' ye bakın.  
Yapılandırma şöyle görünür:  

![Ağ güvenlik gruplarıyla ortak uç noktalara bağlantıyı denetleme](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Önemli noktalar

- Ortak uç noktasına giden bağlantı sağlamak ve maliyeti iyileştirmek için aynı alt ağda birden fazla VM için ek bir genel Load Balancer kullanabilirsiniz  
- VM 'lerden hangi ortak uç noktaların erişilebilir olduğunu denetlemek için [ağ güvenlik gruplarını](../../../virtual-network/network-security-groups-overview.md) kullanın. Ağ güvenlik grubunu alt ağa ya da her bir VM 'ye atayabilirsiniz. Mümkün olduğunda, güvenlik kurallarının karmaşıklığını azaltmak için [hizmet etiketlerini](../../../virtual-network/network-security-groups-overview.md#service-tags) kullanın.  
- Genel IP adresi ve giden kuralları olan Azure Standart yük dengeleyici, genel uç noktasına doğrudan erişim sağlar. Tüm giden trafiğin, denetim ve günlüğe kaydetme için merkezi kurumsal çözümle geçişini sağlamak üzere kurumsal güvenlik gereksinimleriniz varsa, bu senaryoya gereksinimi karşılamayabilir.  

>[!TIP]
>Mümkün olduğunda, ağ güvenlik grubunun karmaşıklığını azaltmak için [hizmet etiketlerini](../../../virtual-network/network-security-groups-overview.md#service-tags) kullanın. 

### <a name="deployment-steps"></a>Dağıtım adımları

1. Load Balancer oluştur  
   1. [Azure Portal](https://portal.azure.com) , tüm kaynaklar ' a tıklayın, ekleyin ve sonra **Load Balancer** arayın  
   1. **Oluştur** seçeneğine tıklayın 
   1. Load Balancer adı **Mypublicilb**  
   1. Tür olarak **genel** ' i SEÇIN, SKU olarak **Standart**  
   1. **Genel IP adresi oluştur** ' u seçin ve ad olarak belirtin **Mypublicilbmindendip**  
   1. Kullanılabilirlik alanı olarak **bölge yedekli** seçeneğini belirleyin  
   1. Gözden geçir ve Oluştur ' a ve ardından Oluştur ' a tıklayın  
2. **Mybackendpoolofpublicılb** arka uç havuzunu oluşturun ve VM 'leri ekleyin.  
   1. Sanal ağı seçin  
   1. VM 'Leri ve IP adreslerini seçin ve arka uç havuzuna ekleyin  
3. [Giden kuralları oluşturun](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). Şu anda Azure portal giden kuralların oluşturulması mümkün değildir. [Azure CLI](../../../cloud-shell/overview.md)ile giden kurallar oluşturabilirsiniz.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Belirli genel uç noktalarına erişimi kısıtlamak için ağ güvenlik grubu kuralları oluşturun. Var olan bir ağ güvenlik grubu varsa, bunu ayarlayabilirsiniz. Aşağıdaki örnekte, Azure Yönetim API 'sine erişimin nasıl etkinleştirileceği gösterilmektedir: 
   1. Ağ güvenlik grubuna git
   1. Giden güvenlik kuralları ' na tıklayın
   1. **Internet**'e giden tüm erişimi **reddetmek** için bir kural ekleyin.
   1. Tüm internet erişimini reddetmek için kuralın önceliğinden daha düşük olan **Azurecı**'ye erişime **izin veren** bir kural ekleyin.


   Giden güvenlik kuralları şöyle görünür: 

   ![Genel IP ile Ikinci Load Balancer giden bağlantı](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Azure ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [güvenlik grupları ](../../../virtual-network/network-security-groups-overview.md). 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>Seçenek 2: internet 'e giden bağlantılar için Azure Güvenlik Duvarı

Ortak uç noktalara giden bağlantılara izin vermeden, genel uç noktalarına giden bağlantı sağlamak için başka bir seçenek de Azure güvenlik duvarıdır. Azure Güvenlik Duvarı, yerleşik yüksek kullanılabilirliğe sahip ve birden çok Kullanılabilirlik Alanları yayılabilen bir yönetilen hizmettir.  
Ayrıca, Azure Güvenlik Duvarı üzerinden trafiği yönlendirmek için VM 'Lerin ve Azure Yük dengeleyicinin dağıtıldığı alt ağla ilişkili [Kullanıcı tanımlı yolu](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)da dağıtmanız gerekir.  
Azure Güvenlik Duvarı 'nı dağıtma hakkında daha fazla bilgi için bkz. [Azure Güvenlik duvarını dağıtma ve yapılandırma](../../../firewall/tutorial-firewall-deploy-portal.md).  

Mimari şöyle görünür:

![Azure Güvenlik Duvarı ile giden bağlantı](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Önemli noktalar

- Azure Güvenlik Duvarı, yerleşik yüksek kullanılabilirliğe sahip bulut Yerel hizmetidir ve bu da bölgesel dağıtımını destekler.
- AzureFirewallSubnet adında olması gereken ek alt ağ gerektirir. 
- SAP VM 'lerinin bulunduğu sanal ağ, başka bir sanal ağdaki bir VM 'ye veya genel uç noktaya giden büyük veri kümelerini aktardıysanız, uygun maliyetli bir çözüm olmayabilir. Bu tür bir örnek, sanal ağlarda büyük yedeklemeleri kopyalamadır. Ayrıntılar için bkz. Azure Güvenlik Duvarı fiyatlandırması.  
- Şirket güvenlik duvarı çözümü Azure Güvenlik Duvarı değilse ve tüm giden trafiğin Merkezi şirket çözümüne geçmesine yönelik güvenlik gereksinimleriniz varsa, bu çözüm pratik olmayabilir.  

>[!TIP]
>Mümkün olduğunda, Azure Güvenlik Duvarı kurallarının karmaşıklığını azaltmak için [hizmet etiketlerini](../../../virtual-network/network-security-groups-overview.md#service-tags) kullanın.  

### <a name="deployment-steps"></a>Dağıtım adımları

1. Dağıtım adımları, VM 'niz için zaten sanal ağ ve alt ağ tanımladığınız varsayılmaktadır.  
2. VM 'lerin ve Standart Load Balancer dağıtıldığı aynı sanal ağda alt ağ **AzureFirewallSubnet** oluşturun.  
   1. Azure portal, sanal ağa gidin: tüm kaynaklar ' a tıklayın, sanal ağı arayın, sanal ağ ' a tıklayın, alt ağlar ' ı seçin.  
   1. Alt ağ Ekle ' ye tıklayın. Ad olarak **AzureFirewallSubnet** girin. Uygun adres aralığını girin. Kaydedin.  
3. Azure Güvenlik duvarı oluşturun.  
   1. Azure portal tüm kaynaklar ' ı seçin, Ekle, güvenlik duvarı, Oluştur ' a tıklayın. Kaynak grubu ' nu seçin (sanal ağın bulunduğu kaynak grubunu seçin).  
   1. Azure Güvenlik Duvarı kaynağı için ad girin. Örneğin, **Myazurefirewall**.  
   1. Bölge ' yi seçin ve VM 'nizin dağıtıldığı kullanılabilirlik bölgeleriyle hizalı en az iki kullanılabilirlik bölgesi seçin.  
   1. SAP VM 'lerinin ve Azure Standart yük dengeleyicinin dağıtıldığı Sanal ağınızı seçin.  
   1. Genel IP adresi: oluştur ' a tıklayın ve bir ad girin. Örneğin, **Myfirewallpublicıp**.  
4. Belirtilen genel uç noktalara giden bağlantılara izin vermek için Azure Güvenlik Duvarı kuralı oluşturun. Örnekte, Azure Yönetim API 'SI genel uç noktasına erişime nasıl izin verilecek gösterilmektedir.  
   1. Kurallar, ağ kuralı koleksiyonu ' nu seçin ve ardından ağ kuralı koleksiyonu Ekle ' ye tıklayın.  
   1. Ad: **mbir Boundrule**, öncelik girin, eylem **izin ver**' i seçin.  
   1. Hizmet: adı **ToAzureAPI**.  Protokol: **herhangi bir** seçin. Kaynak adresi: VM 'Lerin ve Standart Load Balancer örneği için dağıtıldığı alt ağınız için aralığı girin: **11.97.0.0/24**. Hedef bağlantı noktaları: girin <b>*</b> .  
   1. Kaydet
   1. Hala Azure Güvenlik duvarında konumlandırılmakta olduğunuz için genel bakış ' ı seçin. Azure Güvenlik duvarının özel IP adresini aklınızda edin.  
5. Azure Güvenlik Duvarı 'na yol oluşturma  
   1. Azure portal tüm kaynaklar ' ı seçin ve ardından Ekle, rota tablosu, Oluştur ' a tıklayın.  
   1. MyRouteTable adını girin, abonelik, kaynak grubu ve konum ' u seçin (sanal ağınızın ve güvenlik duvarınızın konumuyla eşleşen).  
   1. Kaydet  

   Güvenlik duvarı kuralı şöyle görünür: ![ güvenlik duvarının neye benzebileceklerini gösteren diyagram.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. VM 'nizin alt ağından, **Myazurefirewall** özel IP 'Sinden Kullanıcı tanımlı yol oluşturun.
   1. Yol tablosuna yerleştirdiğiniz gibi rotalar ' ı tıklatın. Ekle ' yi seçin. 
   1. Yol adı: ToMyAzureFirewall, adres ön eki: **0.0.0.0/0**. Sonraki atlama türü: Sanal Gereç seçin. Sonraki atlama adresi: yapılandırdığınız güvenlik duvarının özel IP adresini girin: **11.97.1.4**.  
   1. Kaydet

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Seçenek 3: Azure Yönetim API 'sine pacemaker çağrıları için proxy kullanma

Azure Yönetim API 'SI genel uç noktasına pacemaker çağrılarına izin vermek için proxy kullanabilirsiniz.  

### <a name="important-considerations"></a>Önemli noktalar

  - Zaten şirket proxy 'si varsa, giden çağrıları ortak uç noktalarına yönlendirebilir. Genel uç noktalarına giden çağrılar, kurumsal denetim noktası üzerinden yapılır.  
  - Proxy yapılandırmasının Azure Yönetim API 'sine giden bağlantıya izin verdiğinden emin olun: `https://management.azure.com` ve `https://login.microsoftonline.com`  
  - VM 'lerden ara sunucuya bir yol olduğundan emin olun  
  - Proxy yalnızca HTTP/HTTPS çağrılarını işleymeyecektir. Genel uç noktasına giden çağrıları farklı protokoller üzerinden (RFC gibi) yapmak için ek bir gereksinim varsa, alternatif çözüm gerekecektir  
  - Pacemaker kümesinde kararsızlığa engel olmak için ara sunucu çözümü yüksek oranda kullanılabilir olmalıdır  
  - Proxy 'nin konumuna bağlı olarak, Azure sınır aracılarından Azure Yönetim API 'sine yapılan çağrılarda ek gecikme olabilir. Şirket proxy 'niz hala şirket içinde ise, pacemaker kümeniz Azure 'da, gecikme süresi ölçer ve bu çözüm size uygunsa göz önünde bulundurun.  
  - Zaten yüksek oranda kullanılabilir kurumsal proxy yoksa, müşteri ek maliyet ve karmaşıklık altına alcağından bu seçeneği önermiyoruz. Bununla birlikte, ek proxy çözümü dağıtmaya karar verirseniz, pacemaker 'dan Azure Yönetim ortak API 'sine giden bağlantıya izin vermek için, proxy 'nin yüksek oranda kullanılabilir olduğundan ve VM 'lerden ara sunucuya gecikme olmadığından emin olun.  

### <a name="pacemaker-configuration-with-proxy"></a>Proxy ile paceoluşturucu yapılandırması 

Sektörde kullanılabilen birçok farklı proxy seçeneği vardır. Proxy dağıtımı için adım adım yönergeler, bu belgenin kapsamı dışındadır. Aşağıdaki örnekte, proxy 'nizin **Myproxyservice** 'e yanıt verdiğini ve **myproxyport** bağlantı noktasını dinlediğini varsaytık.  
Pacemaker 'ın Azure Yönetim API 'siyle iletişim kurmasına izin vermek için tüm küme düğümlerinde aşağıdaki adımları gerçekleştirin:  

1. /Etc/sysconfig/pacemaker yapılandırma dosyasını düzenleyin ve aşağıdaki satırları (tüm küme düğümleri) ekleyin:

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. **Tüm** küme düğümlerinde paceyapıcısı hizmetini yeniden başlatın.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>Diğer seçenekler

Giden trafik üçüncü taraf ile yönlendirilmemişse, URL tabanlı güvenlik duvarı proxy 'SI:

- Azure sınır Aracısı 'nı kullanıyorsanız, Güvenlik Duvarı yapılandırmasının Azure Yönetim API 'sine giden bağlantıya izin verdiğinden emin olun: `https://management.azure.com` ve `https://login.microsoftonline.com`   
- SUSE 'un güncelleştirmeleri ve düzeltme eklerini uygulamak için Azure genel bulut güncelleştirme altyapısını kullanıyorsanız bkz. [Azure genel bulut güncelleştirme altyapısı 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da SUSE üzerinde pacemaker 'ı yapılandırma hakkında bilgi edinin](./high-availability-guide-suse-pacemaker.md)
* [Azure 'da, Red hat üzerinde pacemaker 'ı nasıl yapılandıracağınızı öğrenin](./high-availability-guide-rhel-pacemaker.md)
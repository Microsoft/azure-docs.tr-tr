---
title: Kaynak Yöneticisi geçişe klasik VPN Gateway | Microsoft Docs
description: Bu sayfa, Kaynak Yöneticisi geçişe VPN Gateway bir genel bakış sağlar.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488213"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway klasik Kaynak Yöneticisi geçişe
VPN ağ geçitleri artık klasik 'dan Kaynak Yöneticisi dağıtım modeline geçirilebilir. Azure Resource Manager [özellikleri ve avantajları](../azure-resource-manager/management/overview.md)hakkında daha fazla bilgi edinebilirsiniz. Bu makalede, klasik dağıtımlardan daha yeni Kaynak Yöneticisi tabanlı modele nasıl geçirileceğiyle ayrıntılıyoruz. 

VPN ağ geçitleri, klasik bilgisayardan Kaynak Yöneticisi arasında VNet geçişinin parçası olarak geçirilir. Bu geçiş tek seferde bir sanal ağ yapılır. Geçiş için araçlar veya Önkoşullar bakımından ek gereksinim yoktur. Geçiş adımları var olan VNet [geçişiyle aynıdır ve IaaS kaynakları geçiş sayfasında](../virtual-machines/migration-classic-resource-manager-ps.md)belgelenmiştir. Geçiş sırasında veri yolu kapalı kalma süresi yoktur ve bu nedenle mevcut iş yükleri geçiş sırasında şirket içi bağlantı kaybı olmadan çalışmaya devam eder. VPN Gateway ile ilişkili genel IP adresi, geçiş işlemi sırasında değişmez. Bu, geçiş işlemi tamamlandıktan sonra şirket içi yönlendiricinizi yeniden yapılandırmanız gerekeceği anlamına gelir.  

Kaynak Yöneticisi modeli, klasik modelden farklıdır ve sanal ağ geçitleri, yerel ağ geçitleri ve bağlantı kaynaklarından oluşur. Bunlar, şirket içi adres alanını temsil eden yerel-site ve sırasıyla iki arasındaki bağlantıyı temsil eden VPN ağ geçidinin kendisini temsil eder. Geçiş işlemi tamamlandıktan sonra, ağ geçitleriniz Klasik modelde kullanılamaz ve sanal ağ geçitleri, yerel ağ geçitleri ve bağlantı nesnelerinde tüm yönetim işlemleri Kaynak Yöneticisi modeli kullanılarak gerçekleştirilmelidir.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
En yaygın VPN bağlantı senaryoları klasik Kaynak Yöneticisi geçişe göre ele alınmıştır. Desteklenen senaryolar şunlardır-

* Site bağlantısı noktası
* Şirket içi konuma bağlı VPN Gateway siteden siteye bağlantı
* VPN ağ geçitleri kullanan iki sanal ağ arasında VNET 'ten VNet 'e bağlantı
* Aynı şirket içi konuma bağlı birden çok VNET
* Çok siteli bağlantı
* Zorlamalı tünel etkin sanal ağlar

Desteklenmeyen senaryolar şunlardır-  

* Hem ExpressRoute ağ geçidi hem de VPN Gateway sanal ağı şu anda desteklenmemektedir.
* VM uzantılarının şirket içi sunuculara bağlandığı geçiş senaryoları. İletim VPN bağlantısı sınırlamaları aşağıda ayrıntılı olarak verilmiştir.

> [!NOTE]
> Kaynak Yöneticisi modelinde CıDR doğrulaması, klasik modelden daha sıkı bir şekilde. Geçirmeden önce, klasik adres aralıklarının Geçişe başlamadan önce geçerli CıDR biçimine uyduğundan emin olun. CıDR, tüm ortak CıDR Doğrulayıcıları kullanılarak doğrulanabilir. Geçiş sırasında geçersiz CıDR aralıklarına sahip VNet veya yerel siteler başarısız durumuna neden olur.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet 'ten VNet 'e bağlantı geçişi
Klasik VNET 'ten VNet 'e bağlantı, bağlı VNet 'in yerel bir site temsili oluşturularak elde edildi. Müşterilerin birbirine bağlanması gereken iki sanal ağı temsil eden iki yerel site oluşturması gerekiyordu. Bunlar daha sonra, iki sanal ağ arasında bağlantı kurmak için IPSec tüneli kullanılarak ilgili sanal ağlara bağlanmıştı. Bir VNet 'teki herhangi bir adres aralığı değişikliği de karşılık gelen yerel site gösteriminde tutulması gerektiğinden, bu modelde yönetilebilirlik sorunları vardır. Kaynak Yöneticisi modelinde bu geçici çözüm artık gerekli değildir. İki sanal ağ arasındaki bağlantı, bağlantı kaynağındaki ' Vnet2Vnet ' bağlantı türü kullanılarak doğrudan elde edilebilir. 

![VNet 'ten VNet 'e geçiş ekran görüntüsü.](./media/vpn-gateway-migration/migration1.png)

VNet geçişi sırasında, geçerli VNet 'in VPN Gateway 'e bağlı varlığın başka bir VNet olduğunu tespit ettik ve her iki sanal ağın geçişinin tamamlandığından emin olun, artık diğer VNet 'i temsil eden iki yerel site görmezsiniz. İki VPN ağ geçidinin klasik modeli, iki adet yerel site ve aralarında iki bağlantı, iki VPN Gateway ve Vnet2Vnet türünde iki bağlantı olan Kaynak Yöneticisi modele dönüştürülür.

## <a name="transit-vpn-connectivity"></a>İletim VPN bağlantısı
VPN ağ geçitlerini, bir sanal ağa yönelik şirket içi bağlantının, şirket içi olarak doğrudan bağlı olan başka bir sanal ağa bağlanarak elde edilebildiğini bir topolojide yapılandırabilirsiniz. Bu, ilk VNet 'teki örneklerin şirket içi kaynaklara doğrudan şirket içine bağlı olan VNet 'teki VPN ağ geçidine geçiş yoluyla bağlandığı iletim VPN bağlantısı ' dır. Bu yapılandırmayı klasik dağıtım modelinde elde etmek için, hem bağlı VNet 'i hem de şirket içi adres alanını temsil eden toplanmış ön ekleri olan bir yerel site oluşturmanız gerekir. Bu temsili yerel sitesi daha sonra aktarım bağlantısı sağlamak için VNet 'e bağlanır. Bu klasik modelde aynı yönetilebilirlik zorlukları de vardır çünkü şirket içi adres aralığındaki herhangi bir değişiklik aynı zamanda VNet 'in ve şirket içi toplamanın toplamasını temsil eden yerel sitede saklanması gerekir. Desteklenen Kaynak Yöneticisi ağ geçitlerinde BGP desteğinin sunulması, bağlı ağ geçitleri, öneklerde el ile değişiklik yapmadan Şirket içindeki yolları öğrenebileceği için yönetilebilirliği basitleştirir.

![Transit yönlendirme senaryosunun ekran görüntüsü.](./media/vpn-gateway-migration/migration2.png)

Sanal ağı yerel siteler gerekmeden VNet bağlantısına dönüştürtiğimiz için, aktarım senaryosu şirket içinde dolaylı olarak bağlı olan VNet için şirket içi bağlantıyı kaybeder. Bağlantı kaybı, geçiş tamamlandıktan sonra aşağıdaki iki şekilde azaltılabilir. 

* Birlikte ve şirket içine bağlanan VPN ağ geçitlerinde BGP 'yi etkinleştirin. BGP 'nin VNet ağ geçitleri arasında öğrenilmesi ve tanıtılmasından bu yana başka herhangi bir yapılandırma değişikliği yapmadan BGP 'nin yeniden yükleme BGP seçeneğinin yalnızca standart ve daha yüksek SKU 'Larda kullanılabilir olduğunu unutmayın.
* Etkilenen VNet 'ten şirket içi konumu temsil eden yerel ağ geçidine açık bir bağlantı kurun. Ayrıca, IPSec tüneli oluşturmak ve yapılandırmak için şirket içi yönlendiricide yapılandırma değiştirilmesini de gerektirir.

## <a name="next-steps"></a>Sonraki adımlar
VPN Gateway geçiş desteğini öğrendikten sonra, kullanmaya başlamak için [Klasik 'Ten IaaS kaynaklarının, klasik 'ten Kaynak Yöneticisi geçişine](../virtual-machines/migration-classic-resource-manager-ps.md) gidin.
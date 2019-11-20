---
title: "ExpressRoute: devreleri klasik 'dan Azure Resource Manager taşıyın"
description: Klasik ve Resource Manager dağıtım modellerinde köprüleme genel bakış.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr
ms.openlocfilehash: f232e1e8a1f7121b047a846fc2c614b48e757f76
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076651"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>ExpressRoute bağlantı hatlarını klasikten Resource Manager dağıtım modeline taşıma
Bu makale, bir Azure ExpressRoute bağlantı hattını klasikten Azure Resource Manager dağıtım modeline taşmanın ne anlama geldiği hakkında genel bir bakış sağlar.

Her iki klasik ve Resource Manager dağıtım modellerinde dağıtılan sanal ağlara bağlanmak için tek bir ExpressRoute bağlantı hattı kullanabilirsiniz. Bir ExpressRoute bağlantı hattını, nasıl oluşturulduğuna bakmaksızın, artık her iki dağıtım modeli üzerinden sanal ağlara bağlayabilirsiniz.

![Her iki dağıtım modeli üzerinden sanal ağlara bağlanan bir ExpressRoute bağlantı hattı](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Klasik dağıtım modelinde oluşturulmuş ExpressRoute bağlantı hatları
Her iki klasik ve Resource Manager dağıtım modellerine bağlantıyı etkinleştirmek için önce Klasik dağıtım modelinde oluşturulmuş ExpressRoute bağlantı hatlarının Resource Manager dağıtım modeline taşınması gerekir. Bir bağlantı taşınırken bağlantı kaybı veya kesintisi olmaz. Klasik dağıtım modelinde (aynı abonelik ve çapraz abonelik içinde), bağlantı hattından sanal ağa yapılan tüm bağlantılar korunur.

Taşıma başarıyla tamamlandıktan sonra ExpressRoute bağlantı hattı, Resource Manager dağıtım modelinde oluşturulmuş tam bir ExpressRoute bağlantı hattı gibi görünür, gerçekleştirir ve hissettirir. Resource Manager dağıtım modelinde artık sanal ağlara bağlantılar oluşturabilirsiniz.

Bir ExpressRoute bağlantı hattı Resource Manager dağıtım modeline taşındıktan sonra ExpressRoute bağlantı hattı yaşam döngüsünü yalnızca Resource Manager dağıtım modeli kullanarak yönetebilirsiniz. Bu, eşlemeleri ekleme/güncelleştirme/silme, bağlantı hattı özelliklerini güncelleştirme (bant genişliği, SKU ve faturalama türü) ve yalnızca Resource Manager dağıtım modelinde bağlantı hatları silme gibi işlemleri gerçekleştirebileceğiniz anlamına gelir. Her iki dağıtım modeline erişimi yönetebilmeniz hakkında daha fazla ayrıntı için Resource Manager dağıtım modelinde oluşturulan bağlantı hatları ile ilgili aşağıdaki bölüme başvurun.

Taşımayı gerçekleştirmek için bağlantı sağlayıcınızı dahil etmek zorunda değilsiniz.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Resource Manager dağıtım modelinde oluşturulmuş ExpressRoute bağlantı hatları
Resource Manager dağıtım modelinde oluşturulmuş ExpressRoute bağlantı hatlarının her iki dağıtım modelinden erişilebilir olmasını etkinleştirebilirsiniz. Aboneliğinizdeki tüm ExpressRoute bağlantı hatları her iki dağıtım modelinden erişilebilir olarak etkinleştirilebilir.

* Resource Manager dağıtım modelinde oluşturulmuş ExpressRoute bağlantı hatlarının varsayılan olarak klasik dağıtım modeline erişimi yoktur.
* Klasik dağıtım modelinden Resource Manager dağıtım modeline taşınmış ExpressRoute bağlantı hatlarına varsayılan olarak her iki dağıtım modelinden erişilebilir.
* Resource Manager veya klasik dağıtım modelinde oluşturulduğuna bakılmaksızın, bir ExpressRoute bağlantı hattı her zaman Resource Manager dağıtım modeline erişebilir. Bu demek oluyor ki Resource Manager dağıtım modelinde oluşturulan sanal ağlara bağlantılar oluşturabilirsiniz. [Sanal ağlara bağlantı oluşturma](expressroute-howto-linkvnet-arm.md) konusundaki yönergeleri izleyin.
* ExpressRoute bağlantı hattında klasik dağıtım modeline erişim **allowClassicOperations** parametresi tarafından denetlenir.

> [!IMPORTANT]
> [Hizmet sınırlamaları](../azure-subscription-service-limits.md) sayfasında belgelenen tüm kotalar uygulanır. Örnek olarak, standart bir bağlantı hattı her iki klasik ve Resource Manager dağıtım modeli üzerinden en fazla 10 sanal ağ bağlantısına sahip olabilir.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Klasik dağıtım modeline erişimi denetleme
ExpressRoute bağlantı hattının **allowClassicOperations** parametresini ayarlayarak tek bir ExpressRoute bağlantı hattının her iki dağıtım modelinde sanal ağlara bağlanmasını etkinleştirebilirsiniz.

**allowClassicOperations** parametresinin TRUE olarak ayarlanması her iki dağıtım modelinden ExpressRoute bağlantı hattına bağlanmanızı etkinleştirir. [Klasik dağıtım modelinde sanal ağlara bağlanma](expressroute-howto-linkvnet-classic.md) konusundaki kılavuzu izleyerek klasik dağıtım modelinde sanal ağlara bağlantı oluşturabilirsiniz. [Resource Manager dağıtım modelinde sanal ağlara bağlanma](expressroute-howto-linkvnet-arm.md) konusundaki kılavuzu izleyerek Resource Manager dağıtım modelinde sanal ağlara bağlantı oluşturabilirsiniz.

**allowClassicOperations** parametresini FALSE olarak ayarlayarak klasik dağıtım modelinden bağlantı hattına erişimi engeller. Ancak, klasik dağıtım modelinde tüm sanal ağ bağlantıları korunur. Bu durumda, ExpressRoute bağlantı hattı klasik dağıtım modelinde gözükmez.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Klasik dağıtım modelinde desteklenen işlemler
**allowClassicOperations** parametresi TRUE olarak ayarlandığında bir ExpressRoute bağlantı hattı üzerinde şu klasik işlemler desteklenir:

* ExpressRoute bağlantı hattı bilgilerini alma
* Klasik sanal ağlara sanal ağ bağlantıları oluşturma/güncelleştirme/alma/silme
* çapraz abonelik bağlantısı için sanal ağ bağlantı yetkilerini oluşturma/güncelleştirme/alma/silme

Ancak, **allowClassicOperations** olan TRUE olarak ayarlanırsa, şu Klasik işlemleri gerçekleştiremezsiniz:

* Azure özel, Azure genel ve Microsoft eşlemeleri için Sınır Ağ Geçidi Protokolü (BGP) eşlemeleri oluşturma/güncelleştirme/alma/silme
* ExpressRoute bağlantı hatlarını silme

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Klasik ve Resource Manager dağıtım modelleri arasında iletişim
ExpressRoute bağlantı hattı klasik ve Resource Manager dağıtım modelleri arasında bir köprü gibi davranır. Her iki sanal ağ aynı ExpressRoute bağlantı hattına bağlıysa klasik dağıtım modelinde sanal ağlardaki sanal makineler ve Resource Manager dağıtım modelinde sanal ağlardaki sanal makineler arasındaki trafik ExpressRoute aracılığıyla akar.

Toplu işleme, sanal ağa ait ağ geçidi işleme kapasitesi tarafından sınırlandırılmıştır. Bu gibi durumlarda trafik, bağlantı sağlayıcısı ve sizin ağlarınıza girmez. Sanal ağlar arasındaki trafik akışı tam olarak Microsoft ağı içerisinde yer alır.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure genel ve Microsoft eşleme kaynaklarına erişim
Azure genel eşleme ve Microsoft eşleme aracılığıyla normalde erişilebilen kaynaklara erişmeye kesintisiz devam edebilirsiniz.  

## <a name="whats-supported"></a>Desteklenen durumlar
Bu bölümde ExpressRoute bağlantı hatları için desteklenen durumlar açıklanmaktadır:

* Klasik ve Resource Manager dağıtım modellerinde dağıtılan sanal ağlara erişim için tek bir ExpressRoute bağlantı hattı kullanabilirsiniz.
* Bir ExpressRoute bağlantı hattını klasikten Resource Manager dağıtım modeline taşıyabilirsiniz. Taşındıktan sonra, ExpressRoute bağlantı hattı, Resource Manager dağıtım modelinde oluşturulan herhangi diğer ExpressRoute bağlantı hattı gibi görünür, hissettirir ve gerçekleştirir.
* Yalnızca ExpressRoute bağlantı hattını taşıyabilirsiniz. Bağlantı hattı bağlantıları, ağ geçitleri ve VPN ağ geçitleri bu işlem aracılığıyla taşınamaz.
* Bir ExpressRoute bağlantı hattı Resource Manager dağıtım modeline taşındıktan sonra ExpressRoute bağlantı hattı yaşam döngüsünü yalnızca Resource Manager dağıtım modeli kullanarak yönetebilirsiniz. Bu, eşlemeleri ekleme/güncelleştirme/silme, bağlantı hattı özelliklerini güncelleştirme (bant genişliği, SKU ve faturalama türü) ve yalnızca Resource Manager dağıtım modelinde bağlantı hatları silme gibi işlemleri gerçekleştirebileceğiniz anlamına gelir.
* ExpressRoute bağlantı hattı klasik ve Resource Manager dağıtım modelleri arasında bir köprü gibi davranır. Her iki sanal ağ aynı ExpressRoute bağlantı hattına bağlıysa klasik dağıtım modelinde sanal ağlardaki sanal makineler ve Resource Manager dağıtım modelinde sanal ağlardaki sanal makineler arasındaki trafik ExpressRoute aracılığıyla akar.
* Çapraz abonelik bağlantısı her iki klasik ve Resource Manager dağıtım modellerinde desteklenir.
* Bir ExpressRoute bağlantı hattını klasik modelden Azure Resource Manager modeline taşıdıktan sonra [ExpressRoute işlem hattına bağlı sanal ağları geçirebilirsiniz](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Desteklenmeyen durumlar
Bu bölümde ExpressRoute bağlantı hatları için desteklenmeyen durumlar açıklanmaktadır:

* Klasik dağıtım modelinden bir ExpressRoute bağlantı hattının yaşam döngüsünü yönetme.
* Klasik dağıtım modeli için rol tabanlı Access Control (RBAC) desteği. Klasik dağıtım modelinde bağlantı hattına yönelik RBAC denetimleri gerçekleştiremezsiniz. Abonelikteki tüm yöneticiler/yardımcı yöneticiler bağlantı hattına sanal ağları bağlayabilir veya bağlantılarını kaldırabilir.

## <a name="configuration"></a>Yapılandırma
[Bir ExpressRoute bağlantı hattını klasikten Resource Manager dağıtım modeline taşıma](expressroute-howto-move-arm.md) konusunda açıklanan yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [ExpressRoute işlem hattına bağlı sanal ağları klasik modelden Azure Resource Manager modeline geçirme](expressroute-migration-classic-resource-manager.md)
* İş akışı bilgileri için bkz. [ExpressRoute bağlantı hattı sağlama iş akışları ve devre durumları](expressroute-workflows.md).
* ExpressRoute’unuzu yapılandırmak için:
  
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-arm.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md)
  * [ExpressRoute bağlantı hattına bir sanal ağ bağlama](expressroute-howto-linkvnet-arm.md)


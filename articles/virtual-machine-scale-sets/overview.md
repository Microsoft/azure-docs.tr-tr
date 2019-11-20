---
title: Azure sanal makine ölçek kümelerine genel bakış | Microsoft Docs
description: Azure sanal makine ölçek kümeleri ve uygulamalarınızın otomatik olarak ölçeklendirilmesi hakkında bilgi edinin
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: manayar
ms.openlocfilehash: 73580814dcfe8f967684aca4ce433a40e7bbedc0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679380"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri nedir?
Azure sanal makine ölçek kümeleri, bir özdeş, yük dengeli VM 'Ler grubu oluşturmanıza ve yönetmenize olanak sağlar. Sanal makine örneklerinin sayısı isteğe yanıt olarak veya tanımlı bir zamanlamaya göre otomatik olarak artabilir veya azalmış olabilir. Ölçek Kümeleri, uygulamalarınız için yüksek kullanılabilirlik sağlar ve çok sayıda VM 'yi merkezi olarak yönetmenize, yapılandırmanıza ve güncelleştirmenize olanak tanır. Sanal makine ölçek kümeleri ile işlem, büyük veri ve kapsayıcı iş yükleri gibi alanlarda büyük ölçekli hizmetler oluşturabilirsiniz.


## <a name="why-use-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri neden kullanılmalıdır?
Yedeklilik ve gelişmiş performans sağlamak için uygulamalar genellikle birden çok örneğe dağıtılır. Müşteriler, uygulama örneklerinden birine istek dağıtan bir yük dengeleyici aracılığıyla uygulamanıza erişebilir. Bakım yapmanız veya bir uygulama örneğini güncelleştirmeniz gerekiyorsa, müşterilerinizin başka bir kullanılabilir uygulama örneğine dağıtılması gerekir. Ek müşteri talebi ile devam etmek için, uygulamanızı çalıştıran uygulama örneklerinin sayısını artırmanız gerekebilir.

Azure sanal makine ölçek kümeleri, birçok VM genelinde çalışan uygulamalar için yönetim özellikleri, [kaynakların otomatik ölçeklendirilmesi](virtual-machine-scale-sets-autoscale-overview.md)ve trafiğin yük dengelemesi sağlar. Ölçek Kümeleri aşağıdaki önemli avantajları sağlar:

- **Birden çok VM oluşturmak ve yönetmek kolay**
    - Uygulamanızı çalıştıran çok sayıda sanal makine varsa, ortamınızda tutarlı bir yapılandırmanın korunması önemlidir. Uygulamanızın güvenilir performansı için VM boyutu, disk yapılandırması ve uygulama yüklemeleri tüm VM 'lerde eşleşmelidir.
    - Ölçek kümeleri ile, tüm VM örnekleri aynı temel işletim sistemi görüntüsünden ve yapılandırmadan oluşturulur. Bu yaklaşım ek yapılandırma görevleri veya ağ yönetimi olmadan yüzlerce VM 'yi kolayca yönetmenize olanak sağlar.
    - Ölçek Kümeleri, temel katman 4 trafik dağıtımı için [Azure Yük dengeleyicinin](../load-balancer/load-balancer-overview.md) kullanımını ve daha fazla gelişmiş katman 7 trafik DAĞıTıMı ve SSL sonlandırma için [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) kullanımını destekler.

- **Yüksek kullanılabilirlik ve uygulama dayanıklılığı sağlar**
    - Ölçek Kümeleri, uygulamanızın birden çok örneğini çalıştırmak için kullanılır. Bu sanal makine örneklerinden birinde sorun varsa, müşteriler en az kesintiye uğramıştır ve diğer sanal makine örneklerinden biri aracılığıyla uygulamanıza erişmeye devam eder.
    - Ek kullanılabilirlik için, tek bir veri merkezinde veya birden çok veri merkezinde bulunan bir ölçek kümesindeki sanal makine örneklerini otomatik olarak dağıtmak üzere [kullanılabilirlik alanları](../availability-zones/az-overview.md) kullanabilirsiniz.

- **Uygulamanızın, kaynak talebi değişiklikleri olarak otomatik olarak ölçeklendirilmesine izin verir**
    - Uygulamanız için müşteri talebi, gün veya hafta boyunca değişebilir. Müşteri talebini eşleştirmek için ölçek kümeleri, uygulama talebi arttıkça sanal makine örneklerinin sayısını otomatik olarak artırabilir, ardından talep azaldıkça sanal makine örneklerinin sayısını azaltır.
    - Otomatik ölçeklendirme, talep azaldıkça uygulamanızı çalıştıran gereksiz sanal makine örneklerinin sayısını da en aza indirir, müşteriler talep büyüdükçe, kabul edilebilir bir performans düzeyi almaya devam eder ve ek VM örnekleri otomatik olarak eklenir. Bu özellik, maliyetleri azaltmaya ve gerektiğinde Azure kaynaklarını verimli bir şekilde oluşturmanıza yardımcı olur.

- **Büyük ölçekli çalışma**
    - Ölçek Kümeleri 1.000 adede kadar VM örneğini destekler. Kendi özel VM görüntülerinizi oluşturur ve karşıya yüklerseniz, sınır 600 sanal makine örnekleridir.
    - Üretim iş yükleri ile en iyi performansı elde etmek için [Azure yönetilen diskleri](../virtual-machines/windows/managed-disks-overview.md)kullanın.


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Sanal makineler ve ölçek kümeleri arasındaki farklılıklar
Ölçek Kümeleri sanal makinelerden oluşturulur. Ölçek kümeleriyle, uygulamalarınızı çalıştırmak ve ölçeklendirmek için yönetim ve otomasyon katmanları sağlanır. Bunun yerine, tek tek VM 'Leri el ile oluşturup yönetebilir veya benzer bir otomasyon düzeyi oluşturmak için mevcut araçları tümleştirebilirsiniz. Aşağıdaki tabloda, birden fazla sanal makine örneğinin el ile yönetilmesine kıyasla ölçek kümelerinin avantajları özetlenmektedir.

| Senaryo                           | Sanal makinelerin el ile grubu                                                                    | Sanal makine ölçek kümesi |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Ek VM örnekleri ekleme        | El ile oluşturma, yapılandırma ve uyumluluk sağlamaya yönelik işlem                             | Merkezi yapılandırmadan otomatik olarak oluştur |
| Trafik Dengeleme ve dağıtım | Azure yük dengeleyici veya Application Gateway oluşturma ve yapılandırma ile el ile işlem      | Otomatik olarak Azure yük dengeleyici veya Application Gateway oluşturabilir ve tümleştirilebilir |
| Yüksek kullanılabilirlik ve artıklık   | Kullanılabilirlik kümesini el ile oluşturun veya Kullanılabilirlik Alanları genelinde VM 'Leri dağıtın ve izleyin | Kullanılabilirlik Alanları veya kullanılabilirlik kümeleri arasında sanal makine örneklerinin otomatik dağıtımı |
| VM 'lerin ölçeklendirilmesi                     | El ile izleme ve Azure Otomasyonu                                                 | Konak ölçümleri, Konuk içi ölçümler, Application Insights veya zamanlamaya göre otomatik ölçeklendirme |

Ölçek kümelerine ek ücret alınmaz. Yalnızca VM örnekleri, yük dengeleyici veya yönetilen disk depolaması gibi temel işlem kaynakları için ödeme yaparsınız. Otomatik ölçeklendirme ve artıklık gibi yönetim ve otomasyon özellikleri, VM 'lerin kullanımı üzerinde ek ücret vermez.

## <a name="how-to-monitor-your-scale-sets"></a>Ölçek kümelerinizi izleme

Basit bir ekleme işlemine sahip [VM'ler için Azure izleyici](../azure-monitor/insights/vminsights-overview.md)kullanın ve ölçek kümesindeki VM 'LERDEN önemli CPU, bellek, disk ve ağ performans sayaçları koleksiyonunu otomatikleştirin. Ayrıca, ölçek kümelerinizin kullanılabilirliği ve performansına odaklanmaya yardımcı olan ek izleme özellikleri ve önceden tanımlanmış görselleştirmeler de içerir.

Sayfa görünümleri, uygulama istekleri ve özel durumlar dahil olmak üzere uygulamanız hakkında ayrıntılı bilgi toplamak için Application Insights ile [sanal makine ölçek kümesi uygulamanız](../azure-monitor/app/azure-vm-vmss-apps.md) için izlemeyi etkinleştirin. Kullanıcı trafiğinin benzetimini yapmak için bir [Kullanılabilirlik testi](../azure-monitor/app/monitor-web-app-availability.md) yapılandırarak uygulamanızın kullanılabilirliğini daha da doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
Başlamak için Azure portal ilk sanal makine ölçek kümesini oluşturun.

> [!div class="nextstepaction"]
> [Azure portal ölçek kümesi oluşturma](quick-create-portal.md)

---
title: Azure 'da sunucusuz kapsayıcılar
description: Azure Container Instances hizmeti, sanal makinelerin yönetilmesi gerekmeden ve daha üst düzey bir Orchestrator 'ı benimsemek zorunda kalmadan, yalıtılmış kapsayıcıları Azure 'da çalıştırmanın en hızlı ve en kolay yolunu sunar.
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: c445687db7a154b6fc86e962d2c2340ad6297431
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799080"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances nedir?

Bulut uygulamalarını paketlemek, dağıtmak ve yönetmek için kapsayıcılar her geçen gün daha fazla tercih edilmektedir. Azure Container Instances, herhangi bir sanal makineyi yönetmek ve daha yüksek düzeyde bir hizmeti benimsemek zorunda kalmadan Azure içinde kapsayıcı çalıştırmanızın en hızlı ve en kolay yoludur.

Yalıtılmış kapsayıcılarda çalışabileceğiniz senaryolar (basit uygulamalar, görev otomasyonu ve sürüm işleri gibi) için Azure Container Instances harika bir çözümdür. Birden çok kapsayıcıda hizmet bulma, otomatik ölçeklendirme ve eşgüdümlü uygulama yükseltmeleri dahil olmak üzere tam kapsayıcı düzenlemesi gerektiren senaryolar için [Azure Kubernetes hizmeti (AKS)](../aks/index.yml)önerilir.

## <a name="fast-startup-times"></a>Hızlı başlangıç süreleri

Kapsayıcılar, sanal makinelerde (VM) önemli başlangıç süresi avantajları sunar. Azure Container Instances, sanal makineleri sağlamaya ve yönetmeye gerek kalmadan saniyeler içinde Azure’da kapsayıcıları başlatabilir.

Linux veya Windows kapsayıcı görüntülerini Docker Hub 'dan, özel bir [Azure Container Registry](../container-registry/index.yml)'den veya başka bir bulut tabanlı Docker kayıt defterinden taşıyın. ACI tarafından hangi kayıt defterlerinin desteklendiğini öğrenmek için [SSS](container-instances-faq.md) 'yi ziyaret edin. Azure Container Instances, özel uygulama görüntülerinin dağıtımını hızlandırmaya yardımcı olan birkaç ortak temel işletim sistemi görüntüsünü önbelleğe alır.

## <a name="container-access"></a>Kapsayıcı erişimi

Azure Container Instances, kapsayıcı gruplarınızın bir IP adresi ve tam etki alanı adı (FQDN) ile doğrudan internet 'e sunulmasını sağlar. Bir kapsayıcı örneği oluşturduğunuzda, uygulamanızın *customlabel*.*azureregion*.azurecontainer.io üzerinden ulaşılabilir olmasını sağlamak amacıyla bir özel DNS ad etiketi belirleyebilirsiniz.

Azure Container Instances Ayrıca, uygulama geliştirme ve sorun giderme konusunda yardımcı olmak üzere etkileşimli bir kabuk sağlayarak çalışan bir kapsayıcıda bir komutun yürütülmesini destekler. Erişim, istemci bağlantılarını güvenli hale getirmek için TLS kullanarak HTTPS üzerinden yer alır.

> [!IMPORTANT]
> Azure Container Instances 13 Ocak 2020 tarihinden itibaren, sunuculardan ve uygulamalardan gelen tüm güvenli bağlantıların TLS 1,2 kullanması gerekir. TLS 1,0 ve 1,1 desteği kullanımdan kaldırılacak.

## <a name="compliant-deployments"></a>Uyumlu dağıtımlar

### <a name="hypervisor-level-security"></a>Hiper yönetici düzeyinde güvenlik

Geçmişte kapsayıcılar, uygulama bağımlılığı yalıtımı ve kaynak idaresi olanakları sağlıyor ancak birden çok kiracılı zorlu kullanımlar için yeterli kabul edilmiyordu. Azure Container Instances, uygulamanızın bir kapsayıcıda, sanal makinedeki gibi yalıtılmasını sağlar.

### <a name="customer-data"></a>Müşteri verileri

ACı hizmeti, kapsayıcı gruplarınızın beklendiği gibi çalıştığından emin olmak için gereken en düşük müşteri verilerini depolar. Müşteri verilerinin tek bir bölgede depolanması, Brezilya coğrafi bölge 'nin Asya Pasifik coğrafi ve Brezilya Güney (Sao Paulo Eyaleti) bölgesinin Güneydoğu Asya bölgesinde (Singapur) Şu anda kullanılabilir. Diğer tüm bölgeler için müşteri verileri [coğrafi](https://azure.microsoft.com/global-infrastructure/geographies/)olarak depolanır. Daha fazla bilgi için lütfen Azure desteği ile iletişime geçin.

## <a name="custom-sizes"></a>Özel boyutlar

Kapsayıcılar genellikle tek bir uygulamayı çalıştırmak üzere iyileştirilmiştir, ancak söz konusu uygulamaların tam gereksinimleri önemli ölçüde farklı olabilir. Azure Container Instances, CPU çekirdeklerinin ve belleğin tam belirtimlerine imkan vererek en iyi kullanımı sağlar. İhtiyaçlarınıza göre ödeme yapar ve saniye başına faturalandırılırsınız. Böylece harcamalarınızı ihtiyaçlarınıza uyacak şekilde ayarlayabilirsiniz.

Machine Learning gibi işlem yoğunluğu olan işler için Azure Container Instances, Linux kapsayıcılarını NVıDıA Tesla [GPU kaynaklarını](container-instances-gpu.md) (Önizleme) kullanacak şekilde zamanlayabilir.

## <a name="persistent-storage"></a>Kalıcı depolama

Azure Container Instances durumu almak ve kalıcı hale getirmek için Azure depolama tarafından desteklenen [Azure dosya paylaşımlarının doğrudan bağlamasını](./container-instances-volume-azure-files.md) sunuyoruz.

## <a name="linux-and-windows-containers"></a>Linux ve Windows kapsayıcıları

Azure Container Instances, aynı API ile hem Windows hem de Linux kapsayıcıları zamanlayabilir. [Kapsayıcı gruplarınızı](container-instances-container-groups.md) oluştururken işletim sistemi türünü belirmeniz yeterlidir.

Bazı özellikler Şu anda Linux kapsayıcılarıyla kısıtlıdır:

* Kapsayıcı grubu başına birden çok kapsayıcı
* Birim bağlama ([Azure dosyaları](container-instances-volume-azure-files.md), [emptydir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [gizli](container-instances-volume-secret.md))
* Azure Izleyici ile [kaynak kullanımı ölçümleri](container-instances-monitor.md)
* [Sanal ağ dağıtımı](container-instances-vnet.md)
* [GPU kaynakları](container-instances-gpu.md) (Önizleme)

Windows kapsayıcı dağıtımları için, yaygın [Windows temel görüntülerine](container-instances-faq.md#what-windows-base-os-images-are-supported)göre görüntüleri kullanın.

## <a name="co-scheduled-groups"></a>Birlikte zamanlanmış gruplar

Azure Container Instances, bir konak makineyi, yerel ağı, depolamayı ve yaşam döngüsünü paylaşan [çok Kapsayıcılı grupların](container-instances-container-groups.md) planlanmasını destekler. Böylece ana uygulama kapsayıcınızı, günlüğe kaydetme yan bölmesi gibi diğer destekleyici kapsayıcılarla birleştirebilirsiniz.

## <a name="virtual-network-deployment"></a>Sanal ağ dağıtımı

Azure Container Instances [, kapsayıcı örneklerinin bir Azure sanal ağına dağıtılmasını](container-instances-vnet.md)mümkün hale getirmenizi. Sanal ağınız içindeki bir alt ağa dağıtıldığında, kapsayıcı örnekleri şirket içinde olan ( [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-introduction.md)aracılığıyla) sanal ağdaki diğer kaynaklarla güvenli bir şekilde iletişim kurabilir.

## <a name="next-steps"></a>Sonraki adımlar

Hızlı başlangıç kılavuzumuzu kullanarak bir kapsayıcıyı tek bir komutla Azure’a dağıtmayı deneyin:

> [!div class="nextstepaction"]
> [Azure Container Instances Hızlı Başlangıç](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

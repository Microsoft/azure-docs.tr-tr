---
title: Azure Container Instances nedir?
description: Azure Container Instances hizmeti, sanal makinelerin yönetilmesi gerekmeden ve daha üst düzey bir Orchestrator 'ı benimsemek zorunda kalmadan, yalıtılmış kapsayıcıları Azure 'da çalıştırmanın en hızlı ve en kolay yolunu sunar.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/25/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a07eda709c813e68bc3e4f08b6aab3f93dec65a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325754"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances nedir?

Bulut uygulamalarını paketlemek, dağıtmak ve yönetmek için kapsayıcılar her geçen gün daha fazla tercih edilmektedir. Azure Container Instances, herhangi bir sanal makineyi yönetmek ve daha yüksek düzeyde bir hizmeti benimsemek zorunda kalmadan Azure içinde kapsayıcı çalıştırmanızın en hızlı ve en kolay yoludur.

Yalıtılmış kapsayıcılarda çalışabileceğiniz senaryolar (basit uygulamalar, görev otomasyonu ve sürüm işleri gibi) için Azure Container Instances harika bir çözümdür. Eksiksiz bir kapsayıcı düzenlemesi gerektiren senaryolar (birden çok kapsayıcıda hizmet bulma, otomatik ölçeklendirme ve eşgüdümlü uygulama yükseltmeleri gibi) için [Azure Kubernetes Service (AKS)](../aks/index.yml) hizmetini öneririz.

## <a name="fast-startup-times"></a>Hızlı başlangıç süreleri

Kapsayıcılar, sanal makinelerde (VM) önemli başlangıç süresi avantajları sunar. Azure Container Instances, sanal makineleri sağlamaya ve yönetmeye gerek kalmadan saniyeler içinde Azure’da kapsayıcıları başlatabilir.

## <a name="public-ip-connectivity-and-dns-name"></a>Genel IP bağlantısı ve DNS adı

Azure Container Instances, kapsayıcılarınızın İnternet üzerinde bir IP adresi ve tam etki alanı adı (FQDN) ile doğrudan kullanıma sunulmasına olanak sağlar. Bir kapsayıcı örneği oluşturduğunuzda, uygulamanızın *customlabel*.*azureregion*.azurecontainer.io üzerinden ulaşılabilir olmasını sağlamak amacıyla bir özel DNS ad etiketi belirleyebilirsiniz.

## <a name="hypervisor-level-security"></a>Hiper yönetici düzeyinde güvenlik

Geçmişte kapsayıcılar, uygulama bağımlılığı yalıtımı ve kaynak idaresi olanakları sağlıyor ancak birden çok kiracılı zorlu kullanımlar için yeterli kabul edilmiyordu. Azure Container Instances, uygulamanızın bir kapsayıcıda, sanal makinedeki gibi yalıtılmasını sağlar.

## <a name="custom-sizes"></a>Özel boyutlar

Kapsayıcılar genellikle tek bir uygulamayı çalıştırmak üzere iyileştirilmiştir, ancak söz konusu uygulamaların tam gereksinimleri önemli ölçüde farklı olabilir. Azure Container Instances, CPU çekirdeklerinin ve belleğin tam belirtimlerine imkan vererek en iyi kullanımı sağlar. İhtiyaçlarınıza göre ödeme yapar ve saniye başına faturalandırılırsınız. Böylece harcamalarınızı ihtiyaçlarınıza uyacak şekilde ayarlayabilirsiniz.

Machine Learning gibi işlem yoğunluğu olan işler için Azure Container Instances, Linux kapsayıcılarını NVıDıA Tesla [GPU kaynaklarını](container-instances-gpu.md) (Önizleme) kullanacak şekilde zamanlayabilir.

## <a name="persistent-storage"></a>Kalıcı depolama

Azure Container Instances ile durum alma ve durumu kalıcı hale getirme işlemleri için, [Azure Dosyaları paylaşımlarını doğrudan bağlama](container-instances-mounting-azure-files-volume.md) olanağı sunuyoruz.

## <a name="linux-and-windows-containers"></a>Linux ve Windows kapsayıcıları

Azure Container Instances, aynı API ile hem Windows hem de Linux kapsayıcıları zamanlayabilir. [Kapsayıcı gruplarınızı](container-instances-container-groups.md) oluştururken işletim sistemi türünü belirmeniz yeterlidir.

Bazı özellikler Şu anda Linux kapsayıcılarıyla kısıtlıdır:

* Kapsayıcı grubu başına birden çok kapsayıcı
* Birim bağlama ([Azure dosyaları](container-instances-volume-azure-files.md), [emptydir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [gizli](container-instances-volume-secret.md))
* Azure Izleyici ile [kaynak kullanımı ölçümleri](container-instances-monitor.md)
* [Sanal ağ dağıtımı](container-instances-vnet.md) Önizle
* [GPU kaynakları](container-instances-gpu.md) Önizle

Windows kapsayıcı dağıtımları için, yaygın [Windows temel görüntülerine](container-instances-faq.md#what-windows-base-os-images-are-supported)göre görüntüleri kullanın.

> [!NOTE]
> Azure Container Instances 'da Windows Server 2019 tabanlı görüntülerin kullanımı önizlemededir.

## <a name="co-scheduled-groups"></a>Birlikte zamanlanmış gruplar

Azure Container Instances, aynı ana makineyi, yerel ağı, depolama alanını ve yaşam döngüsünü paylaşan [birden çok kapsayıcı grubunun](container-instances-container-groups.md) zamanlanmasını destekler. Böylece ana uygulama kapsayıcınızı, günlüğe kaydetme yan bölmesi gibi diğer destekleyici kapsayıcılarla birleştirebilirsiniz.

## <a name="virtual-network-deployment-preview"></a>Sanal ağ dağıtımı (önizleme)

Şu anda önizleme sürümünde olan bu Azure Container Instances özelliği, [kapsayıcı örneklerinin bir Azure sanal ağına dağıtılmasını sağlar](container-instances-vnet.md). Kapsayıcı örneklerini sanal ağınızdaki bir alt ağa dağıtarak şirket içindekiler dahil olmak üzere ([VPN ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-introduction.md) aracılığıyla) sanal ağ içindeki diğer kaynaklarla güvenli bir şekilde iletişim kurmalarını sağlayabilirsiniz.

> [!IMPORTANT]
> Azure Container Instances belirli özellikleri önizlemede ve bazı [sınırlamalar geçerlidir](container-instances-vnet.md#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliklerin bazı yönleri genel kullanıma (GA) önce değişebilir.

## <a name="next-steps"></a>Sonraki adımlar

Hızlı başlangıç kılavuzumuzu kullanarak bir kapsayıcıyı tek bir komutla Azure’a dağıtmayı deneyin:

> [!div class="nextstepaction"]
> [Azure Container Instances Hızlı Başlangıç](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

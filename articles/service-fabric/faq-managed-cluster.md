---
title: Service Fabric yönetilen kümeler hakkında sık sorulan sorular
description: Yetenekler, kullanım örnekleri ve yaygın senaryolar dahil Service Fabric yönetilen kümeler hakkında sık sorulan sorular.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633096"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric yönetilen kümeler hakkında sık sorulan sorular

Service Fabric yönetilen kümeler (Önizleme) için sık sorulan bazı sorular (SSS) ve yanıtlar aşağıda verilmiştir.

## <a name="general"></a>Genel

### <a name="what-are-service-fabric-managed-clusters"></a>Service Fabric yönetilen kümeler nelerdir?

Service Fabric yönetilen kümeler, kümeleri dağıtmayı ve yönetmeyi kolaylaştırmak için tasarlanan Service Fabric kümesi kaynak modelinin bir gelişmidir. Service Fabric yönetilen bir küme, bir kullanıcının bugün dağıtmaları gereken çok sayıda bağımsız kaynakla (sanal makine ölçek kümesi, Load Balancer, IP ve daha fazlası) yalnızca tek bir küme kaynağı tanımlayıp dağıtabilmesi için Azure Resource Manager kapsülleme modelini kullanır.

### <a name="what-regions-are-supported-in-the-preview"></a>Önizlemede hangi bölgeler destekleniyor?

Service Fabric yönetilen kümeler önizlemesi için desteklenen bölgeler,,,,, `centraluseuap` `eastus2euap` ve içerir `eastasia` `northeurope` `westcentralus` `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Mevcut Service Fabric Kümemin bir yönetilen küme kaynağına yerinde geçişini yapabilir miyim?

Hayır. Bu sırada, yeni Service Fabric yönetilen küme kaynak türünü kullanmak için yeni bir Service Fabric küme kaynağı oluşturmanız gerekir.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric yönetilen kümeler için ek bir ücret var mı?

Hayır. Küme için gereken temel işlem, depolama ve ağ kaynaklarının maliyetinin ötesinde Service Fabric yönetilen bir kümeyle ilişkili ek bir maliyet yoktur.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric yönetilen küme kaynağı tarafından tanıtılan yeni bir SLA var mı?

SLA geçerli Service Fabric kaynak modelinden değişiklik yapmaz.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Temel ve standart SKU kümesi arasındaki fark nedir?

Temel bir SKU kümesi, yapılandırmaların çoğunun Service Fabric kaynak sağlayıcısı tarafından sağlandığı anlamına gelir. Temel SKU kümelerinin test ve üretim öncesi ortamları için kullanılması amaçlanmıştır. Standart SKU kümesi, kullanıcıların, kendi ihtiyaçlarını karşılamak için kümeyi yapılandırmasına olanak sağlar. Daha fazla bilgi için bkz. [yönetilen küme SKU 'ları Service Fabric](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Küme dağıtımı ve yönetimi

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Sanal makine ölçek kümesi 'nde özel Betik uzantıları çalıştırdım, bunu yönetilen bir Service Fabric kaynağıyla yapmaya devam edebilir miyim?

Evet, yönetilen küme düğümü türlerinde VM uzantıları belirtebilirsiniz. Daha fazla bilgi için bkz. [Service Fabric yönetilen küme düğümü türüne ölçek kümesi uzantısı ekleme](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Yalnızca iç yük dengeleyiciye sahip olmak istiyorum mu?

Hayır. Şu anda yalnızca iç yük dengeleyici olması mümkün değildir. İstenmeyen gelen/giden trafiği engellemek için ağ güvenlik grubu (NSG) kurallarını kilitlemeyi öneririz.

### <a name="can-i-autoscale-my-cluster"></a>Kümemi otomatik ölçeklendirme yapabilir miyim?

Otomatik ölçeklendirme Şu anda önizlemede kullanılamıyor.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Kümemi kullanılabilirlik bölgeleri arasında dağıtabilir miyim?

Çapraz kullanılabilirlik bölgesi kümeleri Şu anda önizlemede yok.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Küme çalışma zamanı için otomatik ve el ile yükseltme arasında seçim yapabilir miyim?

Önizlemede, tüm çalışma zamanı yükseltmeleri otomatik olarak tamamlanır.

## <a name="applications"></a>Uygulamalar

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Service Fabric yönetilen kümeler için yerel bir geliştirme deneyimi var mı?

Yerel geliştirme deneyimi, mevcut Service Fabric kümelerinden değişmeden kalır. Daha fazla bilgi için, bkz. yerel geliştirme deneyimi hakkında daha fazla ayrıntı için [geliştirme ortamınızı ayarlama](./service-fabric-get-started.md) .

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Uygulamalarımı bir Azure Resource Manager kaynağı olarak dağıtabilir miyim?

Evet. Uygulamaları Azure Resource Manager bir kaynak olarak dağıtmak için destek eklenmiştir (PowerShell ve CLı kullanarak dağıtıma ek olarak). Başlamak için bkz. [ARM şablonunu kullanarak Service Fabric yönetilen küme uygulaması dağıtma](how-to-managed-cluster-app-deployment-template.md).

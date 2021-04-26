---
title: Azure 'da Red hat iş yükleri genel bakış | Microsoft Docs
description: Azure 'da kullanılabilen Red Hat ürün teklifleri hakkında bilgi edinin.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676060"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 'da Red hat iş yükleri

Red hat iş yükleri, Azure 'daki çeşitli tekliflerle desteklenir. Red Hat Enterprise Linux (RHEL) görüntüleri, Red Hat güncelleştirme altyapısı (RHUı) gibi RHEL iş yüklerinin çekirdeğleridir.

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux görüntüler

Azure, Azure 'da çok sayıda RHEL görüntüsü sunmaktadır. Bu görüntüler iki farklı lisans modeli aracılığıyla kullanılabilir hale getirilir: Kullandıkça öde ve kendi aboneliğinizi getir (BYOS). Azure 'daki yeni RHEL görüntüleri, gerektiğinde yeni RHEL sürümleri yayımlandığında ve bunların yaşam döngüleri boyunca güncelleştirilerek yayımlanır.

### <a name="pay-as-you-go-images"></a>Kullandıkça Öde görüntüleri

Azure, çeşitli RHEL Kullandıkça Öde görüntüleri sunmaktadır. Bu görüntüler RHEL için uygun şekilde bulunur ve bir güncelleştirme kaynağına eklenir (Red Hat güncelleştirme altyapısı). Bu görüntüler RHEL yetkilendirme ve güncelleştirmelerine yönelik bir Premium ücret ödemenizi ister. RHEL Kullandıkça Öde görüntü çeşitleri şunlardır:

* Standart RHEL.
* RHEL for SAP.
* Yüksek kullanılabilirlik ve güncelleştirme hizmetleri olan SAP için RHEL.

Uygun abonelik sayısı için ayrı olarak ödeme yapmak istemiyorsanız, Kullandıkça Öde görüntülerini kullanmak isteyebilirsiniz.

### <a name="red-hat-gold-images"></a>Red Hat Gold resimleri

Azure ayrıca Red Hat Gold görüntüleri ( `rhel-byos` ) sağlar. Bu görüntüler, var olan Red Hat aboneliklerine sahip müşteriler için yararlı olabilir ve bunları Azure 'da kullanmak isteyebilir. Mevcut Red Hat aboneliklerinizi Azure 'da kullanabilmeniz için Red Hat bulut erişimi için etkinleştirmeniz gerekir. Red Hat abonelikleriniz bulut erişimi için etkinleştirildiğinde ve uygunluk gereksinimlerini karşılarken bu görüntülere erişim otomatik olarak verilir. Bu görüntülerin kullanılması, bir müşterinin, Kullandıkça Öde görüntülerinin kullanılmasıyla ilgili bir Double faturalandırma yapmasına engel olabilir.
* [Azure Ile bulut erişimi Için Red Hat aboneliklerinizi nasıl etkinleştireceğinizi](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access)öğrenin.
* [Azure Portal, Azure CLI veya PowerShell cmdlet 'Inde Red Hat altın görüntülerini bulmayı](./byos.md)öğrenin.

> [!NOTE]
> İki faturalandırma, bir kullanıcının RHEL abonelikleri için iki kez ödeme yaptığı durumlarda tahakkuk edilir. Bu senaryo genellikle bir müşteri bir RHEL Kullandıkça Öde sanal makinesine yetkilendirme eklemek için Red Hat Subscription-Manager kullandığında meydana gelir. Örneğin, bir RHEL Kullandıkça Öde görüntüsündeki SAP paketlerine yönelik yetkilendirmeleri eklemek için Subscription-Manager kullanan bir müşteri, RHEL için iki kez ödeme yaptığından, dolaylı olarak çift faturalandırılır. Kullandıkça Öde Premium ücreti üzerinden ve bir kez SAP aboneliğinden bir kez ödeme yapar. Bu senaryo, KCG görüntü kullanıcılarına gerçekleşmez.

### <a name="generation-2-images"></a>2. nesil görüntüler

2. nesil sanal makineler (VM 'Ler) 1. nesil VM 'lere kıyasla daha yeni özellikler sağlar. Daha fazla bilgi için 2. [nesil belgelere](../../generation-2.md)bakın. RHEL görüntü perspektifinden ilgili önemli fark, 2. nesil VM 'Lerin BIOS üretici yazılımı arabirimi yerine UEFı kullanır. Ayrıca önyükleme sırasında ana önyükleme kaydı (MBR) yerine bir GUID bölümleme tablosu (GPT) kullanır. GPT kullanımı, diğer işlerin yanı sıra 2 TB 'tan büyük işletim sistemi disk boyutları için izin verir. Ayrıca, [Mv2 serisi VM 'ler](../../mv2-series.md) yalnızca 2. nesil görüntülerde çalışır.

RHEL 2. nesil görüntüleri Azure Marketi 'nde bulabilirsiniz. Azure CLı kullandığınızda görüntülenen tüm görüntülerin listesindeki görüntü SKU 'sunda "Gen2" öğesini arayın. 2. nesil bir VM dağıtmak için VM dağıtım işlemindeki **Gelişmiş** sekmesine gidin.

## <a name="red-hat-update-infrastructure"></a>Red Hat Güncelleştirme Altyapısı

Azure, yalnızca Kullandıkça Öde RHEL VM 'Leri için Red Hat güncelleştirme altyapısı sağlar. RHUı, Red Hat CDNs 'in bir yansıtmasıdır ancak yalnızca Azure Kullandıkça Öde RHEL VM 'lerinin erişimine açık olur. Dağıttığınız RHEL görüntüsüne bağlı olarak uygun paketlere erişebilirsiniz. Örneğin, bir RHEL for SAP görüntüsü, temel RHEL paketlerine ek olarak SAP paketlerine erişebilir.

### <a name="rhui-update-behavior"></a>RHUı güncelleştirme davranışı

RHUı Update 'e bağlı RHEL görüntüleri, bir çalıştırıldığında RHEL 'nin en son ikincil sürümüne varsayılan olarak bağlanır `yum update` . Bu davranış, üzerinde bir işlem çalıştırıldığında RHEL 7,4 VM 'sinin RHEL 7,7 ' ye yükseltibileceği anlamına gelir `yum update` . Bu davranış, RHUı için tasarımdır. Bu yükseltme davranışını azaltmak için, normal RHEL depolarından [genişletilmiş güncelleştirme destek depolarına](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)geçiş yapın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'Da RHEL görüntüleri](./redhat-images.md)hakkında daha fazla bilgi edinin.
* [Red Hat güncelleştirme altyapısı](./redhat-rhui.md)hakkında daha fazla bilgi edinin.
* [Red Hat Gold Image ( `rhel-byos` ) teklifi](./byos.md)hakkında daha fazla bilgi edinin.
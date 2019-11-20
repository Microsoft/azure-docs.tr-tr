---
title: Azure geçişi ile Azure 'a geçiş için çok sayıda VMware VM 'yi değerlendirin | Microsoft Docs
description: Azure geçişi hizmeti kullanılarak Azure 'a geçiş için çok sayıda VMware VM 'lerinin nasıl değerlendirileneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 0d279781cdc27dbf2140c0100d84de5128c6a3d5
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279420"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Azure 'a geçiş için çok sayıda VMware VM 'lerini değerlendirin


Bu makalede, Azure geçişi sunucu değerlendirmesi Aracı kullanılarak Azure 'a geçiş için şirket içi VMware VM 'lerinin büyük sayılarının (1000-35000) nasıl değerlendirireceğiniz açıklanır.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Ölçek ölçeğinde değerlendirme planlayın.
> * Azure izinlerini yapılandırın ve VMware 'yi değerlendirme için hazırlayın.
> * Bir Azure geçişi projesi oluşturun ve bir değerlendirme oluşturun.
> * Taşımayı planlarken değerlendirmesi gözden geçirin.


> [!NOTE]
> Ölçeği değerlendirmek için birkaç VM 'yi değerlendirmek üzere kavram kanıtı denemek istiyorsanız, [öğretici serimizi](tutorial-prepare-vmware.md) izleyin

## <a name="plan-for-assessment"></a>Değerlendirme planı

Çok sayıda VMware VM değerlendirmesi için planlama yaparken, göz önünde bulundurmanız gereken birkaç nokta vardır:

- **Azure geçişi projelerini planlayın**: Azure geçişi projelerini dağıtmayı öğrenin. Örneğin, Veri merkezleriniz farklı coğrafi bölgelerde ise ya da bulma, değerlendirme veya geçişle ilgili meta verileri farklı bir Coğrafya 'da depolamanız gerekirse, birden çok proje gerekebilir. 
- **Gereçlere plan**: Azure geçişi, sanal makineleri sürekli olarak bulması için VMware VM olarak dağıtılan bir şirket içi Azure geçiş gereci kullanır. Gereç, VM 'Leri, diskleri veya ağ bağdaştırıcılarını ekleme gibi ortam değişikliklerini izler. Ayrıca, Azure 'a bunlarla ilgili meta veriler ve performans verileri de gönderir. Dağıtım yapmanız gereken kaç gereç olduğunu bilmeniz gerekir.
- **Bulma için hesapları planlayın**: Azure geçişi gereci, değerlendirme ve geçiş için VM 'Leri bulmaya yönelik vCenter Server erişimi olan bir hesap kullanır. 10.000 ' den fazla VM keşfetuyorsanız, birden çok hesap ayarlayın.


## <a name="planning-limits"></a>Planlama limitleri
 
Planlama için bu tabloda özetlenen limitleri kullanın.

**Planlama** | **Limitler**
--- | --- 
**Azure geçişi projeleri** | Bir projede en fazla 35.000 VM 'yi değerlendirin.
**Azure geçişi gereci** | Bir gereç, bir vCenter Server en fazla 10.000 VM bulabilir.<br/> Bir gereç yalnızca tek bir vCenter Server bağlanabilir.<br/> Bir gereç, yalnızca tek bir Azure geçişi projesiyle ilişkilendirilebilir.<br/>  Herhangi bir sayıda gereç, tek bir Azure geçişi projesiyle ilişkilendirilebilir. <br/><br/> 
**Grup** | Tek bir gruba en fazla 35.000 VM ekleyebilirsiniz.
**Azure geçişi değerlendirmesi** | Tek bir değerlendirmede 35.000 adede kadar VM 'yi değerlendirebilirsiniz.

Bu limitlerin göz önünde bulundurularak bazı örnek dağıtımlar verilmiştir:


**vCenter sunucusu** | **Sunucudaki VM 'Ler** | **Öneri** | **Eylem**
---|---|---
Biriyle | < 10.000 | Bir Azure geçişi projesi.<br/> Tek gereç.<br/> Bulma için bir vCenter hesabı. | Gereci ayarlama, bir hesapla vCenter Server bağlanma.
Biriyle | > 10.000 | Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Birden çok vCenter hesabı. | Her 10.000 VM için gereç ayarlayın.<br/><br/> VCenter hesaplarını ayarlayın ve bir hesabın erişimini 10.000 ' den az VM 'ye sınırlamak için envanteri bölün.<br/> Her bir gereci bir hesapla vCenter Server 'a bağlayın.<br/> Farklı gereçlerle keşfedilen makineler arasında bağımlılıkları çözümleyebilirsiniz.
Birden Çok | < 10.000 |  Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Bulma için bir vCenter hesabı. | Gereçlerini ayarlama, bir hesapla vCenter Server bağlama.<br/> Farklı gereçlerle keşfedilen makineler arasında bağımlılıkları çözümleyebilirsiniz.
Birden Çok | > 10.000 | Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Birden çok vCenter hesabı. | VCenter Server bulma < 10.000 VM 'Leri varsa, her vCenter Server için bir gereç kurun.<br/><br/> VCenter Server bulma > 10.000 VM 'Ler için, her 10.000 sanal makine için bir gereç kurun.<br/> VCenter hesaplarını ayarlayın ve bir hesabın erişimini 10.000 ' den az VM 'ye sınırlamak için envanteri bölün.<br/> Her bir gereci bir hesapla vCenter Server 'a bağlayın.<br/> Farklı gereçlerle keşfedilen makineler arasında bağımlılıkları çözümleyebilirsiniz.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Çok kiracılı bir ortamda bulmayı planlayın

Çok kiracılı bir ortam için planlama yapıyorsanız, vCenter Server bulmayı kapsam içinde tanımlayabilirsiniz.

- Gereç bulma kapsamını bir vCenter Server veri merkezi, kümeler veya küme klasörü, ana bilgisayarlar veya konaklar klasörü veya ayrı VM 'Ler olarak ayarlayabilirsiniz.
- Ortamınız kiracılar arasında paylaşılmışsa ve her kiracıyı ayrı olarak bulmak istiyorsanız, gerecin bulma için kullandığı vCenter hesabına erişim kapsamını belirleyebilirsiniz. 
    - Kiracılar Konakları paylaşıyorsa VM klasörlerine göre kapsam yapmak isteyebilirsiniz. VCenter hesabının vCenter VM klasör düzeyinde erişimi varsa Azure geçişi VM 'Leri bulamaz. Bulma işlemini VM klasörlerine göre yapmak istiyorsanız, vCenter hesabının bir VM düzeyinde salt okuma erişimi olduğundan emin olarak bunu yapabilirsiniz. Kapsam [bulma hakkında](tutorial-assess-vmware.md#scoping-discovery)daha fazla bilgi edinin.

## <a name="prepare-for-assessment"></a>Değerlendirme için hazırlanma

Sunucu değerlendirmesi için Azure ve VMware 'yi hazırlayın. 

1. [VMware desteği gereksinimlerini ve sınırlamalarını](migrate-support-matrix-vmware.md)doğrulayın.
2. Azure hesabınız için Azure geçişi ile etkileşime geçmek üzere izinleri ayarlayın.
3. VMware 'i değerlendirme için hazırlayın.

Bu ayarları yapılandırmak için [Bu öğreticideki](tutorial-prepare-vmware.md) yönergeleri izleyin.


## <a name="create-a-project"></a>Proje oluşturma

Planlama gereksinimlerinize uygun olarak şunları yapın:

1. Bir Azure geçişi projesi oluşturun.
2. Azure geçişi sunucu değerlendirmesi aracını projelere ekleyin.

[Daha fazla bilgi edinin](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Değerlendirme oluşturma ve gözden geçirme

1. VMware VM 'Leri için değerlendirmeler oluşturun.
1. Geçiş planlaması hazırlığı sırasında değerlendirmeleri gözden geçirin.


Bu ayarları yapılandırmak için [Bu öğreticideki](tutorial-assess-vmware.md) yönergeleri izleyin.
    

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları yapacaksınız:
 
> [!div class="checklist"] 
> * VMware VM 'Leri için Azure geçişi değerlendirmelerinin ölçeklendirilmesi planlanmaktadır
> * Değerlendirme için Azure ve VMware hazırlandı
> * Bir Azure geçişi projesi oluşturdunuz ve değerlendirmeler çalıştırıldı
> * Geçişe hazırlanmayla ilgili değerlendirmeler gözden geçirildi.

Şimdi, değerlendirmelerin nasıl hesaplanacağını ve [değerlendirmelerin nasıl değiştirileceğini](how-to-modify-assessment.md) [öğrenin](concepts-assessment-calculation.md) .

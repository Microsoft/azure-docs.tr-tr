---
title: Azure geçişi ile Azure 'a geçiş için VMware ortamındaki çok sayıda sunucuyu değerlendirin
description: Azure geçişi hizmeti kullanılarak Azure 'a geçiş için VMware ortamındaki çok sayıda sunucuyu nasıl değerlendireceğinizi açıklar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778262"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Azure 'a geçiş için VMware ortamındaki çok sayıda sunucuyu değerlendirin


Bu makalede, Azure geçişi bulma ve değerlendirme aracı kullanılarak Azure 'a geçiş için VMware ortamındaki şirket içi sunucuların büyük sayılarının (1000-35000) nasıl değerlendiriyapılacağı açıklanır.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Ölçek ölçeğinde değerlendirme planlayın.
> * Azure izinlerini yapılandırın ve VMware 'yi değerlendirme için hazırlayın.
> * Bir Azure geçişi projesi oluşturun ve bir değerlendirme oluşturun.
> * Taşımayı planlarken değerlendirmesi gözden geçirin.


> [!NOTE]
> Ölçeği değerlendirmek için birkaç sunucuyu değerlendirmek üzere bir kavram kanıtı denemek istiyorsanız, [öğretici serimizi](./tutorial-discover-vmware.md) izleyin

## <a name="plan-for-assessment"></a>Değerlendirme planı

VMware ortamında çok sayıda sunucu değerlendirmesi için planlama yaparken göz önünde bulundurmanız gereken birkaç nokta vardır:

- **Azure geçişi projelerini planlayın**: Azure geçişi projelerinin nasıl dağıtılacağını öğrenin. Örneğin, Veri merkezleriniz farklı coğrafi bölgelerde ise ya da bulma, değerlendirme veya geçişle ilgili meta verileri farklı bir Coğrafya 'da depolamanız gerekirse, birden çok proje gerekebilir. 
- **Gereçler planı**: Azure geçişi, sunucuları sürekli olarak bulmaya yönelik BIR VMware VM olarak dağıtılan şirket Içi bir Azure geçiş gereci kullanır. Gereç, sunucu, disk veya ağ bağdaştırıcısı ekleme gibi ortam değişikliklerini izler. Ayrıca, Azure 'a bunlarla ilgili meta veriler ve performans verileri de gönderir. Dağıtım yapmanız gereken kaç gereç olduğunu bilmeniz gerekir.
- **Kayıtları bulma Için planlayın**: Azure geçişi gereci, sunucuları değerlendirme ve geçiş için bulmak üzere vCenter Server erişimi olan bir hesap kullanır. 10.000 'den fazla sunucu keşfederseniz, bir projedeki herhangi bir iki gereçde bulunan sunucular arasında çakışma olmaması gerektiği için birden çok hesap ayarlayın. 

> [!NOTE]
> Birden çok gereç ayarlıyorsanız, belirtilen vCenter hesaplarındaki sunucular arasında çakışma olmadığından emin olun. Böyle bir çakışmayla bulma işlemi desteklenmeyen bir senaryodur. Bir sunucu birden fazla gereç tarafından keşfedildiğinde, bu, sunucu geçişinin Azure portal kullanarak sunucu için çoğaltmayı etkinleştirirken bulma ve sorunlara yineleme ile sonuçlanır.

## <a name="planning-limits"></a>Planlama limitleri
 
Planlama için bu tabloda özetlenen limitleri kullanın.

**Planlama** | **Sınırlar**
--- | --- 
**Azure geçişi projeleri** | Bir projede en fazla 35.000 sunucuyu değerlendirin.
**Azure Geçişi gereci** | Bir gereç, bir vCenter Server en fazla 10.000 sunucu bulabilir.<br/> Bir gereç yalnızca tek bir vCenter Server bağlanabilir.<br/> Bir gereç, yalnızca tek bir Azure geçişi projesiyle ilişkilendirilebilir.<br/>  Herhangi bir sayıda gereç, tek bir Azure geçişi projesiyle ilişkilendirilebilir. <br/><br/> 
**Grup** | Tek bir gruba en fazla 35.000 sunucu ekleyebilirsiniz.
**Azure geçişi değerlendirmesi** | Tek bir değerlendirmede en fazla 35.000 sunucu değerlendirebilirsiniz.

Bu limitlerin göz önünde bulundurularak bazı örnek dağıtımlar verilmiştir:


**vCenter server** | **Sunucudaki sunucular** | **Öneri** | **Eylem**
---|---|---|---
Bir | < 10.000 | Bir Azure geçişi projesi.<br/> Tek gereç.<br/> Bulma için bir vCenter hesabı. | Gereci ayarlama, bir hesapla vCenter Server bağlanma.
Bir | > 10.000 | Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Birden çok vCenter hesabı. | Her 10.000 sunucu için gereç ayarlayın.<br/><br/> VCenter hesaplarını ayarlayın ve bir hesabın erişimini 10.000 ' dan az sunucusuna sınırlamak için envanteri bölün.<br/> Her bir gereci bir hesapla vCenter Server 'a bağlayın.<br/> Farklı gereçlerle keşfedilen sunucular arasında bağımlılıkları çözümleyebilirsiniz. <br/> <br/> Belirtilen vCenter hesaplarındaki sunucular arasında çakışma olmadığından emin olun. Böyle bir çakışmayla bulma işlemi desteklenmeyen bir senaryodur. Bir sunucu birden fazla gereç tarafından keşfedildiğinde, bu, bulma ve problemteki bir çoğaltmayla sonuçlanır ve sunucu geçişinin Azure portal kullanarak sunucu için çoğaltmayı etkinleştirir.
Birden çok | < 10.000 |  Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Bulma için bir vCenter hesabı. | Gereçlerini ayarlama, bir hesapla vCenter Server bağlama.<br/> Farklı gereçlerle keşfedilen sunucular arasında bağımlılıkları çözümleyebilirsiniz.
Birden çok | > 10.000 | Bir Azure geçişi projesi.<br/> Birden çok gereç.<br/> Birden çok vCenter hesabı. | VCenter Server bulma < 10.000 sunucuları için her vCenter Server için bir gereç kurun.<br/><br/> VCenter Server bulma > 10.000 sunucuları için, her 10.000 sunucu için bir gereç kurun.<br/> VCenter hesaplarını ayarlayın ve bir hesabın erişimini 10.000 ' dan az sunucusuna sınırlamak için envanteri bölün.<br/> Her bir gereci bir hesapla vCenter Server 'a bağlayın.<br/> Farklı gereçlerle keşfedilen sunucular arasında bağımlılıkları çözümleyebilirsiniz. <br/><br/> Belirtilen vCenter hesaplarındaki sunucular arasında çakışma olmadığından emin olun. Böyle bir çakışmayla bulma işlemi desteklenmeyen bir senaryodur. Bir sunucu birden fazla gereç tarafından keşfedildiğinde, bu, bulma ve problemteki bir çoğaltmayla sonuçlanır ve sunucu geçişinin Azure portal kullanarak sunucu için çoğaltmayı etkinleştirir.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Çok kiracılı bir ortamda bulmayı planlayın

Çok kiracılı bir ortam için planlama yapıyorsanız, vCenter Server bulmayı kapsam içinde tanımlayabilirsiniz.

- Gereç bulma kapsamını bir vCenter Server veri merkezi, kümeler veya küme klasörü, konaklar veya konaklar klasörü veya ayrı sunucular olarak ayarlayabilirsiniz.
- Ortamınız kiracılar arasında paylaşılmışsa ve her kiracıyı ayrı olarak bulmak istiyorsanız, gerecin bulma için kullandığı vCenter hesabına erişim kapsamını belirleyebilirsiniz. 
    - Kiracılar Konakları paylaşıyorsa VM klasörlerine göre kapsam yapmak isteyebilirsiniz. VCenter hesabının vCenter VM klasör düzeyinde erişimi varsa Azure geçişi sunucuları bulamaz. Bulma işlemini VM klasörlerine göre atamak istiyorsanız, vCenter hesabının sunucu düzeyinde salt okuma erişimi olduğundan emin olmak için bunu yapabilirsiniz. [Daha fazla bilgi edinin](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Değerlendirme için hazırlanma

Azure ve VMware 'yi bulma ve değerlendirme aracı için hazırlayın:

1. [VMware desteği gereksinimlerini ve sınırlamalarını](migrate-support-matrix-vmware.md)doğrulayın.
2. Azure hesabınız için Azure geçişi ile etkileşime geçmek üzere izinleri ayarlayın.
3. VMware 'i değerlendirme için hazırlayın.

Bu ayarları yapılandırmak için [Bu öğreticideki](./tutorial-discover-vmware.md) yönergeleri izleyin.


## <a name="create-a-project"></a>Proje oluşturma

Planlama gereksinimlerinize uygun olarak şunları yapın:

1. Bir Azure geçişi projesi oluşturun.
2. Azure geçişi bulma ve değerlendirme aracını projelere ekleyin.

[Daha fazla bilgi edinin](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Değerlendirme oluşturma ve gözden geçirme

1. VMware ortamındaki sunucular için değerlendirmeler oluşturun.
1. Geçiş planlaması hazırlığı sırasında değerlendirmeleri gözden geçirin.


Bu ayarları yapılandırmak için [Bu öğreticideki](./tutorial-assess-vmware-azure-vm.md) yönergeleri izleyin.
    

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları yapacaksınız:
 
> [!div class="checklist"] 
> * VMware ortamındaki sunucular için Azure geçişi değerlendirmelerinin ölçeklendirilmesi planlanmaktadır
> * Değerlendirme için Azure ve VMware hazırlandı
> * Bir Azure geçişi projesi oluşturdunuz ve değerlendirmeler çalıştırıldı
> * Geçişe hazırlanmayla ilgili değerlendirmeler gözden geçirildi.

Şimdi, değerlendirmelerin nasıl hesaplanacağını ve [değerlendirmelerin nasıl değiştirileceğini](how-to-modify-assessment.md) [öğrenin](concepts-assessment-calculation.md) .
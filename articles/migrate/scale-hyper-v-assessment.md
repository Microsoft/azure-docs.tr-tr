---
title: Azure geçişi ile Azure 'a geçiş için çok sayıda Hyper-V VM 'yi değerlendirin | Microsoft Docs
description: Azure geçişi hizmeti kullanılarak Azure 'a geçiş için çok sayıda Hyper-V sanal makinesi değerlendirme işlemini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279448"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Azure 'a geçiş için çok sayıda Hyper-V VM 'yi değerlendirin

Bu makalede, Azure geçişi sunucu değerlendirmesi Aracı kullanılarak Azure 'a geçiş için çok sayıda şirket içi Hyper-V sanal makinelerinden nasıl değerlendirireceğiniz açıklanır.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 


Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Ölçek ölçeğinde değerlendirme planlayın.
> * Azure izinlerini yapılandırın ve Hyper-V ' i değerlendirme için hazırlayın.
> * Bir Azure geçişi projesi oluşturun ve bir değerlendirme oluşturun.
> * Taşımayı planlarken değerlendirmesi gözden geçirin.


> [!NOTE]
> Ölçeği değerlendirmek için birkaç VM 'yi değerlendirmek üzere kavram kanıtı denemek istiyorsanız, [öğretici serimizi](tutorial-prepare-hyper-v.md) izleyin

## <a name="plan-for-assessment"></a>Değerlendirme planı

Çok sayıda Hyper-V VM değerlendirmesi için planlama yaparken göz önünde bulundurmanız gereken birkaç nokta vardır:

- **Azure geçişi projelerini planlayın**: Azure geçişi projelerini dağıtmayı öğrenin. Örneğin, Veri merkezleriniz farklı coğrafi bölgelerde ise ya da bulma, değerlendirme veya geçişle ilgili meta verileri farklı bir Coğrafya 'da depolamanız gerekirse, birden çok proje gerekebilir.
- **Gereçlere plan**: Azure geçişi, bir Hyper-V VM olarak dağıtılan bir şirket içi Azure geçiş gereci kullanarak değerlendirme ve geçiş için sanal makineleri sürekli olarak bulur. Gereç, VM 'Leri, diskleri veya ağ bağdaştırıcılarını ekleme gibi ortam değişikliklerini izler. Ayrıca, Azure 'a bunlarla ilgili meta veriler ve performans verileri de gönderir. Dağıtım için kaç gereç belirlemeniz gerekir.


## <a name="planning-limits"></a>Planlama limitleri
 
Planlama için bu tabloda özetlenen limitleri kullanın.

**Planlama** | **Limitler**
--- | --- 
**Azure geçişi projeleri** | Bir projede en fazla 35.000 VM 'yi değerlendirin.
**Azure geçişi gereci** | Bir gereç, en fazla 5000 VM bulabilir.<br/> Bir gereç, 300 adede kadar Hyper-V konaklarına bağlanabilir.<br/> Bir gereç, yalnızca tek bir Azure geçişi projesiyle ilişkilendirilebilir.<br/> Herhangi bir sayıda gereç, tek bir Azure geçişi projesiyle ilişkilendirilebilir. <br/><br/> 
**Grup** | Tek bir gruba en fazla 35.000 VM ekleyebilirsiniz.
**Azure geçişi değerlendirmesi** | Tek bir değerlendirmede 35.000 adede kadar VM 'yi değerlendirebilirsiniz.



## <a name="other-planning-considerations"></a>Diğer planlama konuları

- Gereci bulmayı başlatmak için, her bir Hyper-V konağını seçmeniz gerekir. 
- Çok kiracılı bir ortam çalıştırıyorsanız, şu anda yalnızca belirli bir kiracıya ait olan VM 'Leri keşfedesiniz. 

## <a name="prepare-for-assessment"></a>Değerlendirme için hazırlanma

Sunucu değerlendirmesi için Azure ve Hyper-V ' i hazırlayın. 

1. [Hyper-V destek gereksinimlerini ve sınırlamalarını](migrate-support-matrix-hyper-v.md)doğrulayın.
2. Azure hesabınız için Azure geçişi ile etkileşime geçmek üzere izinleri ayarlama
3. Hyper-V konakları ve VM 'Leri hazırlama

Bu ayarları yapılandırmak için [Bu öğreticideki](tutorial-prepare-hyper-v.md) yönergeleri izleyin.

## <a name="create-a-project"></a>Proje oluşturma

Planlama gereksinimlerinize uygun olarak şunları yapın:

1. Bir Azure geçişi projesi oluşturun.
2. Azure geçişi sunucu değerlendirmesi aracını projelere ekleyin.

[Daha fazla bilgi edinin](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Değerlendirme oluşturma ve gözden geçirme

1. Hyper-V VM 'Leri için değerlendirmeler oluşturun.
1. Geçiş planlaması hazırlığı sırasında değerlendirmeleri gözden geçirin.

Değerlendirmeler oluşturma ve gözden geçirme hakkında [daha fazla bilgi edinin](tutorial-assess-hyper-v.md) .
    

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları yapacaksınız:
 
> [!div class="checklist"] 
> * Hyper-V VM 'Leri için Azure geçişi değerlendirmelerinin ölçeklendirilmesi planlanmaktadır
> * Değerlendirme için Azure ve Hyper-V hazırlandı
> * Bir Azure geçişi projesi oluşturdunuz ve değerlendirmeler çalıştırıldı
> * Geçişe hazırlanmayla ilgili değerlendirmeler gözden geçirildi.

Şimdi, değerlendirmelerin nasıl hesaplanacağını ve [değerlendirmelerin nasıl değiştirileceğini](how-to-modify-assessment.md) [öğrenin](concepts-assessment-calculation.md)

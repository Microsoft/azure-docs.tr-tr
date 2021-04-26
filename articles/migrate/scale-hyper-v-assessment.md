---
title: Azure geçişi ile Azure 'a geçiş için Hyper-V ortamındaki çok sayıda sunucuyu değerlendirin | Microsoft Docs
description: Azure geçişi hizmeti kullanılarak Azure 'a geçiş için Hyper-V ortamındaki çok sayıda sunucunun nasıl değerlendirileneceğini açıklar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780302"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Azure 'a geçiş için Hyper-V ortamındaki çok sayıda sunucuyu değerlendirin

Bu makalede, Azure geçişi bulma ve değerlendirme aracı kullanılarak Azure 'a geçiş için Hyper-V ortamındaki çok sayıda şirket içi sunucuyu değerlendirmek açıklanır.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 


Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Ölçek ölçeğinde değerlendirme planlayın.
> * Azure izinlerini yapılandırın ve Hyper-V ' i değerlendirme için hazırlayın.
> * Bir Azure geçişi projesi oluşturun ve bir değerlendirme oluşturun.
> * Taşımayı planlarken değerlendirmesi gözden geçirin.


> [!NOTE]
> Ölçeği değerlendirmek için birkaç sunucuyu değerlendirmek üzere bir kavram kanıtı denemek istiyorsanız, [öğretici serimizi](./tutorial-discover-hyper-v.md) izleyin

## <a name="plan-for-assessment"></a>Değerlendirme planı

Hyper-V ortamında çok sayıda sunucu değerlendirmesi için planlama yaparken göz önünde bulundurmanız gereken birkaç nokta vardır:

- **Azure geçişi projelerini planlayın**: Azure geçişi projelerinin nasıl dağıtılacağını öğrenin. Örneğin, Veri merkezleriniz farklı coğrafi bölgelerde ise ya da bulma, değerlendirme veya geçişle ilgili meta verileri farklı bir Coğrafya 'da depolamanız gerekirse, birden çok proje gerekebilir.
- **Gereçlerde plan** yapın: Azure geçişi, bir Hyper-V VM olarak dağıtılan bir şirket Içi Azure geçiş gereci kullanarak, değerlendirme ve geçiş için sunucuları sürekli olarak bulur. Gereç, sunucu, disk veya ağ bağdaştırıcısı ekleme gibi ortam değişikliklerini izler. Ayrıca, Azure 'a bunlarla ilgili meta veriler ve performans verileri de gönderir. Dağıtım için kaç gereç belirlemeniz gerekir.


## <a name="planning-limits"></a>Planlama limitleri
 
Planlama için bu tabloda özetlenen limitleri kullanın.

**Planlama** | **Sınırlar**
--- | --- 
**Azure geçişi projeleri** | Bir projede en fazla 35.000 sunucuyu değerlendirin.
**Azure Geçişi gereci** | Bir gereç, en fazla 5000 sunucuyu bulabilir.<br/> Bir gereç, 300 adede kadar Hyper-V konaklarına bağlanabilir.<br/> Bir gereç, yalnızca tek bir Azure geçişi projesiyle ilişkilendirilebilir.<br/> Herhangi bir sayıda gereç, tek bir Azure geçişi projesiyle ilişkilendirilebilir. <br/><br/> 
**Grup** | Tek bir gruba en fazla 35.000 sunucu ekleyebilirsiniz.
**Azure geçişi değerlendirmesi** | Tek bir değerlendirmede en fazla 35.000 sunucu değerlendirebilirsiniz.



## <a name="other-planning-considerations"></a>Diğer planlama konuları

- Gereci bulmayı başlatmak için, her bir Hyper-V konağını seçmeniz gerekir. 
- Çok kiracılı bir ortam çalıştırıyorsanız, şu anda yalnızca belirli bir kiracıya ait olan sunucuları bulamayız. 

## <a name="prepare-for-assessment"></a>Değerlendirme için hazırlanma

Azure ve Hyper-V ' i bulma ve değerlendirme aracı için hazırlayın: 

1. [Hyper-V destek gereksinimlerini ve sınırlamalarını](migrate-support-matrix-hyper-v.md)doğrulayın.
2. Azure hesabınız için Azure geçişi ile etkileşime geçmek üzere izinleri ayarlama
3. Hyper-V konakları ve sunucuları hazırlama

Bu ayarları yapılandırmak için [Bu öğreticideki](./tutorial-discover-hyper-v.md) yönergeleri izleyin.

## <a name="create-a-project"></a>Proje oluşturma

Planlama gereksinimlerinize uygun olarak şunları yapın:

1. Bir Azure geçişi projesi oluşturun.
2. Azure geçişi bulma ve değerlendirme aracını projelere ekleyin.

[Daha fazla bilgi edinin](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Değerlendirme oluşturma ve gözden geçirme

1. Hyper-V ortamındaki sunucular için değerlendirmeler oluşturun.
1. Geçiş planlaması hazırlığı sırasında değerlendirmeleri gözden geçirin.

Değerlendirmeler oluşturma ve gözden geçirme hakkında [daha fazla bilgi edinin](tutorial-assess-hyper-v.md) .
    

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları yapacaksınız:
 
> [!div class="checklist"] 
> * Hyper-V ortamındaki sunucular için Azure geçişi değerlendirmelerinin ölçeklendirilmesi planlanmaktadır
> * Değerlendirme için Azure ve Hyper-V hazırlandı
> * Bir Azure geçişi projesi oluşturdunuz ve değerlendirmeler çalıştırıldı
> * Geçişe hazırlanmayla ilgili değerlendirmeler gözden geçirildi.

Şimdi, değerlendirmelerin nasıl hesaplanacağını ve [değerlendirmelerin nasıl değiştirileceğini](how-to-modify-assessment.md) [öğrenin](concepts-assessment-calculation.md)
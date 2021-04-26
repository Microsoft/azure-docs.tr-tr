---
title: Azure için Avere vFXT
description: Veri yoğun yüksek performanslı bilgi işlem görevleri için bulut tabanlı bir dosya sistemi önbelleğe alma çözümü olan Azure için avere vFXT hakkında bilgi edinin.
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 5095f05b0c9ce9061781c3558ca6c7091ef37aa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270965"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Azure için Avere vFXT nedir?

Azure için Avere vFXT, yoğun veri kullanan yüksek performanslı bilgi işlem (HPC) görevlerine yönelik bir dosya sistemi önbellek çözümüdür. Bulut bilişimin ölçeklenebilirlik avantajlarından faydalanarak şirket içi donanımlarınızda bulunan veriler dahil olmak üzere tüm verilerinize ihtiyaç duyulduğu yerde ve zamanda erişim sağlanmasını mümkün kılar.

Avere vFXT şu yaygın bilgi işlem senaryolarını destekler:

* Karma bulut mimarisi-Azure için avere vFXT, dosyaları taşımaya gerek kalmadan bulut bilgi işlem avantajını sağlayan bir donanım depolama sistemiyle çalışabilir.

* Azure için bulut patlaması-avere vFXT, verilerinizi tek bir proje için buluta taşımanızı veya tüm iş akışının kalıcı olarak "kaldırın ve kaydırmasını" sağlayabilir.

![Blob depolama alanına ve şirket içi veri merkezine bağlı bir Azure aboneliğindeki Avere vFXT sisteminin ayrıntılarını gösteren diyagram](media/avere-vfxt-hybrid.png)

Azure için avere vFXT, bu durumlar için idealdir:

* HPC iş yükleri için yoğun okuma işlemleri
* Ortak NFS protokolünü kullanan uygulamalar
* 1000-40.000 CPU çekirdeğine sahip işlem grupları
* Şirket içi NAS donanımı, Azure Blob depolama alanı veya ikisiyle birden tümleştirme

Daha fazla bilgi için <https://azure.microsoft.com/services/storage/avere-vfxt/> adresini ziyaret edin.

## <a name="who-uses-avere-vfxt-for-azure"></a>Azure için Avere vFXT kimlere yöneliktir?

Avere vFXT, okuma açısından yoğun bilgi işlem görevlerinde yardımcı olabilir:

### <a name="visual-effects-rendering"></a>Görsel efekt oluşturma

Avere vFXT kümesi medya ve eğlence sektöründe zaman açısından kritik projeler için veri erişimini hızlandırabilir. Azure'da daha fazla önbellek alanı ve işlem düğümü ekleme imkanına sahip olduğunuz için büyük projeleri verimli bir şekilde işleyebilirsiniz.

### <a name="life-sciences"></a>Yaşam bilimleri

Avere vFXT, araştırmacıların Azure Işlem 'da ikincil analiz iş akışlarını çalıştırmasına ve konumlarına bakılmaksızın genomıc verilerine erişmesine olanak tanır.

İlaç araştırmada avere vFXT kümeleri, araştırmacıların ilaç-Target etkileşimlerini tahmin etmesine ve araştırma verilerini çözümlemesine yardımcı olarak ilaç bulmayı hızlandırabilir.

### <a name="financial-services-analytics"></a>Finansal hizmetler analizi

Avere vFXT kümesi nicel analiz hesaplamalarını hızlandırmaya yardımcı olarak finansal kuruluşlara stratejik karar alma konusunda daha iyi içgörüler sunabilir.

## <a name="features-and-specifications"></a>Özellikler ve belirtimler

Avere vFXT sistemi, bir küme halinde yapılandırılmış üç veya daha fazla sanal kenar dosya düğümünden oluşur. İstemci makinelerin yanında konumlandırılarak doğrudan depolama alanını takmak yerine kümeyi takma seçeneği sunar.

Avere vFXT kümesi, istenen dosyaları önbelleğe alır. Tekrarlanan istekler %80'den daha fazla önbellekten sunulabilir.

### <a name="compatibility"></a>Uyumluluk

* NetApp veya Dell EMC Isilon donanımsal NAS sistemleri ile uyumludur
* Azure Blob ile uyumludur
* NFSv3 veya SMB2 protokolünü kullanır

Azure için avere vFXT aşağıdaki Azure kaynaklarını kullanır:

|Azure bileşeni| Kaynak |
|----------|-----------|
|Sanal makineler|3 veya daha fazla E32s_v3|
|Premium SSD depolama alanı|200 GB işletim sistemi alanına ek olarak düğüm başına 1 TB-4 TB önbellek alanı |
|Depolama hesabı (isteğe bağlı) |v2|
|Veri arka uç depolama alanı (isteğe bağlı) | Boş bir LRS Blob kapsayıcısı |

## <a name="next-steps"></a>Sonraki adımlar

Azure dağıtımı için kendi avere vFXT 'nizi planlamak ve oluşturmak için bu makaleleri okuyun.

* [Sisteminizi planlayın](avere-vfxt-deploy-plan.md)
* [Dağıtıma genel bakış](avere-vfxt-deploy-overview.md)
* [vFXT ortamını oluşturma](avere-vfxt-deploy.md)

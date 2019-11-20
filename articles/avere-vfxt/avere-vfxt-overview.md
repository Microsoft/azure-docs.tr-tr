---
title: Azure için avere vFXT
description: HPC için bir bulut önbelleği katmanı olan Azure için avere vFXT 'ye giriş
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 70ae20daa81ab52d4054dcd4bea3c9432a5ceaeb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256164"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Azure için avere vFXT nedir? 

Azure için avere vFXT, veri yoğun yüksek performanslı bilgi işlem (HPC) görevlerine yönelik bir dosya sistemi önbelleğe alma çözümüdür. Kendi şirket içi donanımınızda depolanan veriler için bile gerektiğinde verilerinizi erişilebilir hale getirmek için bulut bilgi işlemin ölçeklenebilirliğini avantajlarından yararlanmanızı sağlar.

Avere vFXT, bu ortak bilgi işlem senaryolarını destekler: 

* Karma bulut mimarisi: Azure için avere vFXT, dosyaları taşımaya gerek kalmadan bulut bilgi işlemin avantajını sağlayan bir donanım depolama sistemiyle çalışabilir. 
* Bulut patlaması: Azure için avere vFXT, verilerinizi tek bir proje için buluta taşımanızı veya tüm iş akışının kalıcı olarak "kaldırın ve kaydırmasını" sağlayabilir. 

![Blob depolamaya ve şirket içi veri merkezine bağlı bir Azure aboneliğinin içindeki avere vFXT sisteminin ayrıntılarını gösteren diyagram](media/avere-vfxt-hybrid.png)

Azure için avere vFXT, bu durumlar için idealdir: 

* HPC iş yükleri için okuma ağır işlemleri
* Ortak NFS protokolünü kullanan uygulamalar
* 1000 ile 40.000 arasındaki CPU çekirdekleri için işlem grupları
* Şirket içi donanım NAS, Azure Blob depolama veya her ikisiyle tümleştirme

Daha fazla bilgi için @no__t ziyaret edin-0

## <a name="who-uses-avere-vfxt-for-azure"></a>Azure için avere vFXT 'yi kimler kullanıyor? 

Avere vFXT, tüm okuma yoğunluklu bilgi işlem görevleri konusunda yardımcı olabilir:

### <a name="visual-effects-rendering"></a>Görsel etkileri işleme 

Medya ve eğlence sürümünde avere vFXT kümesi, zaman açısından kritik işleme projeleri için veri erişimini hızlandırabilir. Azure 'da daha fazla önbellek alanı ve daha fazla işlem düğümü ekleyebildiğinden, büyük projeleri verimli bir şekilde işleme esnekliği de vardır. 

### <a name="life-sciences"></a>Yaşam Bilimleri 

Avere vFXT, araştırmacıların Azure Işlem 'da ikincil analiz iş akışlarını çalıştırmasına ve konumlarına bakılmaksızın genomıc verilerine erişmesine izin verebilir.

İlaç araştırmada avere vFXT kümeleri, araştırmacıların ilaç-Target etkileşimlerini tahmin etmesine ve araştırma verilerini çözümlemesine yardımcı olarak ilaç bulmayı hızlandırmak için kullanılabilir.

### <a name="financial-services-analytics"></a>Finans hizmetleri Analizi

Avere vFXT kümesi, finansal hizmetler şirketlerinin stratejik kararlar almaya yönelik daha iyi Öngörüler sağlayan nicel Analysis hesaplamalarını hızlandırmaya yardımcı olabilir. 

## <a name="features-and-specifications"></a>Özellikler ve belirtimler

Avere vFXT sistemi, bir kümede yapılandırılmış üç veya daha fazla sanal Edge filme düğümünden oluşur. Bu, depolamayı doğrudan bağlamak yerine kümeyi bağlayan istemci makinelerinin yakınında bulunabilir. 

Avere vFXT kümesi dosyaları istendiği gibi önbelleğe alır. Yinelenen istekler, önbellekten% 80 ' dan fazla bir süre içinde sunulabilir.

### <a name="compatibility"></a>Uyumluluk 

* NetApp veya Dell EMC ıınon 'tan donanım NAS sistemleriyle uyumlu
* Azure blob ile uyumlu
* NFSv3 veya SMB2 protokolünü kullanır

Avere vFXT aşağıdaki Azure kaynaklarını kullanır: 

|Azure bileşeni|   |
|----------|-----------|
|Sanal makineler|3 veya daha fazla E32s_v3|
|Premium SSD depolama|200 GB işletim sistemi alanı ve düğüm başına 1 TB-4 TB önbellek alanı |
|Depolama hesabı (isteğe bağlı) |v2|
|Veri arka ucu depolaması (isteğe bağlı) | Bir boş LRS blob kapsayıcısı |

## <a name="next-steps"></a>Sonraki adımlar

Kendi avere vFXT dağıtımınızı oluşturmaya başlamak için bazı bağlantılar aşağıda verilmiştir. 

* [Sisteminizi planlama](avere-vfxt-deploy-plan.md)
* [Dağıtıma genel bakış](avere-vfxt-deploy-overview.md)
* [VFXT oluşturma](avere-vfxt-deploy.md)

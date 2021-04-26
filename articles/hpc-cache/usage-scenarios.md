---
title: Azure HPC önbellek senaryoları
description: Bilgi işlem işinizin Azure HPC önbelleğiyle iyi çalışıp çalışmadığını nasıl bildiğinizi açıklar
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260007"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>İşiniz Azure HPC önbelleğine uygun mu?

Azure HPC önbelleği, çok sayıda disiplindeki yüksek performanslı bilgi işlem işleri için verilere erişimi hızlandırabilir. Ancak her iş akışı türü için kusursuz değildir. Bu makalede, HPC önbelleğinin gereksinimleriniz için iyi bir seçenek olup olmadığına karar verme yönergeleri sunulmaktadır.

[Genel bakış](hpc-cache-overview.md) makalesi, Azure HPC önbelleğinin ne zaman kullanılacağına ve bazı kullanım örneklerinin bazı örneklerine ilişkin kısa bir ana hat de sağlar.

Ayrıca, Önizlemedeki, [NFS 'e bağlı BLOB depolama](../storage/blobs/network-file-system-protocol-support.md)'nın etkili bir şekilde kullanılmasını sağlama hakkındaki [Bu makaleyi](nfs-blob-considerations.md) okuyun.

## <a name="nfs-version-30-applications"></a>NFS sürüm 3,0 uygulamaları

Azure HPC Cache yalnızca NFS 3,0 istemcilerini destekler.

## <a name="high-read-to-write-ratio"></a>Yüksek okuma/yazma oranı

İşlem istemcilerinin yazma işlemleri sırasında daha fazla okuma yaptıkları iş yükleri genellikle bir önbellek için iyi adaylardır. Örneğin, okuma/yazma oranınızdan 80/20 veya 70/30 olması halinde, Azure HPC Cache, bunları uzaktan depolamadan ve üzerinden getirmek zorunda kalmak yerine, sık istenen dosyaları önbellekten sunmaya yardımcı olabilir.

Bir dosyayı getirmek ve ilk kez önbellekte depolamak, normal bir istemci isteğine doğrudan depolamaya kısa bir gecikme süresine sahiptir, bu nedenle bir istemci aynı dosyayı istediğinde verimlilik artışı gelir. Bu, özellikle büyük dosyalar için geçerlidir. Her istemci isteği benzersizdir, HPC önbelleğinin etkisi sınırlı olur. Ancak dosya büyüdükçe, bu ilk erişimden sonra daha iyi performans zaman içinde olur.

## <a name="file-based-analytic-workload"></a>Dosya tabanlı analitik iş yükü

Azure HPC Cache, dosya tabanlı verileri kullanan bir işlem hattı için idealdir ve özellikle işlem istemcileri Azure sanal makinelerinizde çok sayıda işlem istemcisi üzerinde çalışır. Uzun dosya erişim süreleriyle kaynaklanan yavaş veya tutarsız performansı gidermeye yardımcı olabilir.

## <a name="remote-data-access"></a>Uzak veri erişimi

İş yükünüzün, bilgi işlem kaynaklarına daha yakın taşınamayacak uzak verilere erişmesi gerekiyorsa, Azure HPC önbelleği gecikme süresini azaltmaya yardımcı olabilir. Örneğin, kayıtlarınız WAN ortamının en sonunda, farklı bir Azure bölgesinde veya bir müşteri veri merkezinde olabilir. (Bu bazen "dosya-burdıya" olarak adlandırılır.)

## <a name="heavy-request-load"></a>Ağır istek yükü

Çok sayıda istemci kaynaktan aynı anda veri talebinde bulunursa, Azure HPC Cache dosya erişimini hızlandırabilir. Örneğin, yüksek performanslı bir bilgi işlem kümesiyle kullanıldığında, Azure HPC Cache, önbellek aracılığıyla yüksek sayıda eşzamanlı istek için ölçeklenebilirlik sağlar.

## <a name="compute-resources-are-located-in-azure"></a>İşlem kaynakları Azure 'da bulunur

Azure sanal makineleri, yüksek performanslı bilgi işlem iş yüküne ölçeklenebilir ve uygun maliyetli bir yanıttır. Azure HPC önbelleği, özellikle özgün veriler uzak bir sistemde depolanıyorsa, bunlara yakın gereksinim duydukları bilgileri vererek yardımcı olabilir.

Bir müşteri, Azure sanal makinelerinde geçerli işlem hattını "olduğu gibi" çalıştırmak isterse, Azure HPC Cache, ölçeklenebilirlik için POSIX tabanlı bir paylaşılan depolama (veya önbelleğe alma) çözümü sağlayabilir.

Azure HPC önbelleğini kullanarak, Azure Blob depolama 'ya yerel çağrılar yapmak için iş ardışık düzenini yeniden mimarmeniz gerekmez. Verilerinize özgün sisteminde erişebilirsiniz veya HPC cache kullanarak yeni bir blob kapsayıcısına taşıyabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Genel bakış](hpc-cache-overview.md) ve [Önkoşullar](hpc-cache-prerequisites.md) makalelerinde bir önbelleğin nasıl planlanacağı ve yapılandırılacağı hakkında daha fazla bilgi edinin
* Azure HPC Cache ile [NFS etkin BLOB depolama](nfs-blob-considerations.md) (Önizleme) kullanma konuları

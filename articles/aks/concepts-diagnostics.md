---
title: Azure Kubernetes hizmeti (AKS) tanılamaları genel bakış
description: Azure Kubernetes hizmetinde kendi kendine tanılama kümeleri hakkında bilgi edinin.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011567"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes hizmet tanılama (Önizleme) genel bakış

Azure Kubernetes Service (AKS) kümesi sorunları sorunlarını giderme, özellikle kümeniz iş açısından kritik iş yükleri çalıştırıyorsa kümenizin korunmasında önemli bir rol oynar. AKS Tanılaması, şu şekilde olan akıllı ve kendi kendine tanılama deneyimidir:
* Kümenizdeki sorunları belirlemenize ve çözmenize yardımcı olur. 
* Bulutu yereldir.
* Ek yapılandırma veya fatura maliyeti gerektirmez.

Bu özellik artık genel önizlemeye sunuldu. 

## <a name="open-aks-diagnostics"></a>AKS tanılamayı aç

AKS tanılama 'ya erişmek için:

1. [Azure Portal](https://portal.azure.com)Kubernetes kümenize gidin.
1. Sol gezinti bölmesinde, AKS tanılamayı açan **sorunları Tanıla ve çöz** ' e tıklayın.
1. Küme _düğümü sorunları_ gibi, kümenizin sorunu en iyi şekilde açıklayan bir kategori seçin:
    * Giriş sayfası kutucuğunda anahtar sözcükler kullanılıyor.
    * Arama çubuğundaki sorununuzu en iyi şekilde açıklayan bir anahtar sözcük yazın.

![Giriş sayfası](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Tanılama raporunu görüntüle

Bir kategoriye tıkladıktan sonra, kümenize özgü bir tanılama raporu görüntüleyebilirsiniz. Tanılama raporları, durum simgeleriyle kümenizdeki tüm sorunları akıllıca çağırır. Ayrıntılı bir açıklama görmek için **daha fazla bilgi** ' ye tıklayarak her konuda ayrıntıya gidebilirsiniz:
* Sorunlar
* Önerilen Eylemler
* Faydalı docs bağlantıları
* İlgili ölçümler
* Verileri günlüğe kaydetme 

Tanılama raporları, çeşitli denetimler çalıştırıldıktan sonra kümenizin geçerli durumuna bağlı olarak oluşturulur. Bunlar, kümenizin sorununu işaret etmek ve sorunu çözmek için sonraki adımları anlamak için yararlı olabilir.

![Tanılama raporu](./media/concepts-diagnostics/diagnostic-report.png)

![Genişletilmiş tanılama raporu](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Küme öngörüleri

Aşağıdaki tanılama denetimleri, **küme öngörülerinde** kullanılabilir.

### <a name="cluster-node-issues"></a>Küme düğümü sorunları

Küme düğümü sorunları, kümenizin beklenmedik şekilde davranmasına neden olan düğümle ilgili sorunları denetler.

- Düğüm hazırlığı sorunları
- Düğüm sorunları
- Yetersiz kaynak
- Düğüm eksik IP yapılandırması
- Düğüm CNı arızaları
- Düğüm bulunamadı
- Düğüm gücü kapalı
- Düğüm kimlik doğrulama hatası
- Node kuin-proxy eski

### <a name="create-read-update--delete-crud-operations"></a>Oluşturma, okuma, güncelleştirme & silme (CRUD) işlemleri

CRUD Işlemleri, kümenizde sorunlara neden olan CRUD işlemlerini denetler.

- Kullanımdaki alt ağ silme işlemi hatası
- Ağ güvenlik grubu silme işlemi hatası
- Kullanımdaki yol tablosu silme işlemi hatası
- Başvurulan kaynak sağlama hatası
- Genel IP adresi silme işlemi hatası
- Dağıtım kotası nedeniyle dağıtım hatası
- Kuruluş ilkesi nedeniyle işlem hatası
- Abonelik kaydı eksik
- VM Uzantısı sağlama hatası
- Alt ağ kapasitesi
- Kota aşıldı hatası

### <a name="identity-and-security-management"></a>Kimlik ve güvenlik yönetimi

Kimlik ve güvenlik yönetimi, kümenizin iletişimini önleyen kimlik doğrulama ve yetkilendirme hatalarını algılar.

- Düğüm yetkilendirme sorunları
- 401 hata
- 403 hataları

## <a name="next-steps"></a>Sonraki adımlar

* [Aks Periscope](https://aka.ms/aksperiscope)kullanarak küme sorunlarınızı gidermeye yardımcı olmak için günlükleri toplayın.

* AKS gün-2 işlemler kılavuzunun [önceliklendirme uygulamaları bölümünü](/azure/architecture/operator-guides/aks/aks-triage-practices) okuyun.

* Başlığa "[diag]" ekleyerek sorularınızı veya görüşlerinizi [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) 'a gönderin.
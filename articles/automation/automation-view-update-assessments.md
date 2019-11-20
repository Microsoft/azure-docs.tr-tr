---
title: Azure Güncelleştirme Yönetimi güncelleştirme değerlendirmelerini görüntüleme
description: Bu makalede, güncelleştirme dağıtımları için güncelleştirme değerlendirmelerinin nasıl görüntüleneceği açıklanır.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4e20b2924504b714dff9f5ba650f9b25c1c26c3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690856"
---
# <a name="view-azure-update-management-update-assessments"></a>Azure Güncelleştirme Yönetimi güncelleştirme değerlendirmelerini görüntüleme

Azure Otomasyonu hesabınızda, makinelerinizin durumunu görüntülemek için **güncelleştirme yönetimi** ' yi seçin.

Bu görünüm, makineleriniz, eksik güncelleştirmeler, güncelleştirme dağıtımları ve zamanlanmış güncelleştirme dağıtımları hakkında bilgi sağlar. **Uyumluluk** sütununda, makinenin en son değerlendirildiğini görebilirsiniz. **GÜNCELLEŞTIRME ARACıSı hazırlığı** sütununda, güncelleştirme aracısının sistem durumunu görebilirsiniz. Bir sorun varsa, sorunu düzeltmenize yardımcı olabilecek sorun giderme belgelerine gitmek için bağlantıyı seçin.

Makine, güncelleştirme veya dağıtım hakkında bilgi döndüren bir günlük araması çalıştırmak için, listeden karşılık gelen öğeyi seçin. **Günlük araması** bölmesi, seçilen öğe için bir sorgu ile açılır:

![Güncelleştirme Yönetimi varsayılan görünüm](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Eksik güncelleştirmeleri görüntüle

Makinelerinizde bulunmayan güncelleştirmelerin listesini görüntülemek için **eksik güncelleştirmeler** ' i seçin. Her güncelleştirme listelenir ve seçilebilir. Güncelleştirme, işletim sistemi ayrıntıları ve daha fazla bilgi için bir bağlantının gerekli olduğu makine sayısı hakkında bilgi gösterilir. **Günlük araması** bölmesinde güncelleştirmeler hakkında daha fazla ayrıntı de gösterilmektedir.

![Eksik güncelleştirmeler](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Güncelleştirme sınıflandırmaları

Aşağıdaki tablolar, Güncelleştirme Yönetimi ' deki güncelleştirme sınıflandırmalarını her sınıflandırma için bir tanım ile listeler.

### <a name="windows"></a>Windows

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler     | Kritik, güvenlikle ilgili olmayan bir hatayı ele alan belirli bir sorun için güncelleştirme.        |
|Güvenlik güncelleştirmeleri     | Ürüne özgü, güvenlikle ilgili bir sorun için bir güncelleştirme.        |
|Güncelleştirme paketleri     | Kolay dağıtım için bir arada paketlenmiş toplu bir düzeltme kümesi.        |
|Özellik paketleri     | Ürün sürümü dışında dağıtılan yeni ürün özellikleri.        |
|Hizmet paketleri     | Bir uygulamaya uygulanan toplu bir düzeltme kümesi.        |
|Tanım güncelleştirmeleri     | Virüs veya diğer tanım dosyalarına yönelik bir güncelleştirme.        |
|Araçlar     | Bir veya daha fazla görevi tamamlamaya yardımcı olan bir yardımcı program veya özellik.        |
|Güncellemeler     | Şu anda yüklü olan bir uygulama veya dosyaya yönelik bir güncelleştirme.        |

### <a name="linux-2"></a>'Un

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler ve güvenlik güncelleştirmeleri     | Belirli bir sorun veya ürüne özgü, güvenlikle ilgili bir sorun için güncelleştirmeler.         |
|Diğer güncelleştirmeler     | Doğası gereği önemli olmayan veya güvenlik güncelleştirmeleri olmayan diğer tüm güncelleştirmeler.        |

Linux için Güncelleştirme Yönetimi, değerlendirme verilerini görüntülerken buluttaki kritik güncelleştirmeleri ve güvenlik güncelleştirmelerini ayırt edebilir. (Bulutta veri zenginleştirme nedeniyle bu ayrıntı düzeyi mümkündür.) Düzeltme eki uygulama Güncelleştirme Yönetimi makinede bulunan sınıflandırma verilerine bağımlıdır. Diğer dağıtımlardan farklı olarak, CentOS ürünün RTM sürümlerinde bu bilgiler bulunmamaktadır. Aşağıdaki komut için güvenlik verilerini döndürecek şekilde yapılandırılmış CentOS makineleriniz varsa, sınıflandırmalara göre Güncelleştirme Yönetimi düzeltme eki uygulanabilir:

```bash
sudo yum -q --security check-update
```

Şu anda yerel sınıflandırmanın etkinleştirilmesi için desteklenen bir yöntem yok-CentOS üzerinde veri kullanılabilirliği. Şu anda, bu işlevselliği kendi kendilerine etkinleştirmiş olan müşterilere yalnızca en iyi çaba desteği sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

Güncelleştirme değerlendirmelerini güncelleştirdikten sonra, [Azure sanal makinelerinize yönelik güncelleştirmeleri ve düzeltme eklerini yönetme](automation-tutorial-update-management.md)adımlarını izleyerek bir güncelleştirme dağıtımı zamanlayabilirsiniz.

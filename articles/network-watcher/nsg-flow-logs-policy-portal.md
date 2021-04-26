---
title: Hızlı başlangıç-Azure Ilkesi kullanarak NSG akış günlüklerini dağıtma ve yönetme
titleSuffix: Azure Network Watcher
description: Bu makalede, NSG akış günlüklerinin dağıtımını yönetmek için yerleşik ilkelerin nasıl kullanılacağı açıklanır.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011110"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Hızlı başlangıç: Azure Ilkesi kullanarak NSG akış günlüklerini dağıtma ve yönetme 

## <a name="overview"></a>Genel Bakış
Azure İlkesi, kuruluş standartlarının uygulanmasına ve büyük ölçekte uyumluluk değerlendirmesi yapılmasına yardımcı olur. Azure Ilkesi için genel kullanım örnekleri, kaynak tutarlılığı, mevzuata uyumluluk, güvenlik, maliyet ve yönetim için idare uygulamayı içerir. Bu makalede, akış günlükleri kurulumunuzu yönetmek için NSG akış günlükleri için kullanılabilen iki yerleşik ilke kullanacağız. İlk ilke, akış günlükleri etkin olmayan tüm NSG 'leri işaretler. İkinci ilke, akış günlükleri etkin olmadan NSG 'ler için akış günlüklerini otomatik olarak dağıtır. 

Azure ilkesini ilk kez oluşturuyorsanız, şu şekilde okuyabilirsiniz: 
- [Azure İlkesi'ne genel bakış](../governance/policy/overview.md) 
- [İlke oluşturma öğreticisi](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>İlkeleri bulma
1. Azure portal git- [Portal.Azure.com](https://portal.azure.com) 

Üst arama çubuğu ![ Ilkesi giriş sayfasında ilkeyi arayarak Azure ilke sayfasına gidin](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Sol bölmedeki **atamalar** sekmesine gidin

![Atamalar sekmesi](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. **Ilke ata** düğmesine tıklayın 

![Ilke ata düğmesi](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Kullanılabilir ilkeleri görmek için "Ilke tanımları" altında üç nokta menüsüne tıklayın

5. Tür filtresini kullanın ve "yerleşik" i seçin. Ardından "akış günlüğü" araması yapın

Akış günlükleri ilke listesi için iki yerleşik ilke görmeniz gerekir ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Atamak istediğiniz ilkeyi seçin

- *"Akış günlüğü her ağ güvenlik grubu için yapılandırılmalıdır"* , akış günlüğü etkin olmayan NSG 'ler olan, uyumsuz NSG 'leri işaret eden denetim ilkesidir
- *"Hedef ağ güvenlik grubu ile akış günlüğü kaynağı dağıtma"* ilkesi, bir dağıtım eylemi olan Ilkedir, akış günlükleri olmadan tüm NSG 'lerdeki akış günlüklerine izin verebilir

Aşağıdaki her ilke için ayrı yönergeler vardır.  

## <a name="audit-policy"></a>Denetim İlkesi 

### <a name="how-the-policy-works"></a>İlkenin nasıl çalıştığı

İlke, "Microsoft. Network/networkSecurityGroups" türündeki tüm mevcut ARM nesnelerini denetler, bu, belirli bir kapsamdaki tüm NSG 'lere bakar ve NSG akış günlükleri özelliği aracılığıyla bağlantılı akış günlüklerinin varlığını denetler. Özellik yoksa NSG işaretlenir.

İlkenin tam tanımını görmek isterseniz, bu ilkeyi bulmak için [tanımlar sekmesini](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) ziyaret edebilir ve "akış günlükleri" araması yapabilirsiniz

### <a name="assignment"></a>Atama

1. İlke ayrıntılarınızı girin

- Kapsam: abonelik ortak bir seçimdir, sizin için uygun olan bir yönetim grubu veya kaynak grubu da seçebilirsiniz.  
- İlke tanımı: "ilkeleri bulma" bölümünde gösterildiği gibi seçilmelidir.
- Atamaadı: açıklayıcı bir ad seçin 

2. Atamanız gözden geçirmek için "gözden geçir + oluştur" seçeneğine tıklayın

İlke için herhangi bir parametre gerekmez. Bir denetim ilkesi atarken, "Düzeltme" sekmesinde ayrıntıları doldurmanız gerekmez.  

![Denetim Ilkesi Incelemesi](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Sonuçlar

Sonuçları denetlemek için, Uyumluluk sekmesini açın ve atamalarınızın adını arayın.
İlkeniz çalıştıktan sonra aşağıdaki ekran görüntüsüne benzer bir şey görmeniz gerekir. İlkenizin çalıştırılmadığı durumlarda bir süre bekleyin. 

![Denetim Ilkesi sonuçları](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>-If-NOT EXISTS Ilkesi 

### <a name="policy-structure"></a>İlke yapısı

İlke, "Microsoft. Network/networkSecurityGroups" türündeki tüm mevcut ARM nesnelerini denetler, bu, belirli bir kapsamdaki tüm NSG 'lere bakar ve NSG akış günlükleri özelliği aracılığıyla bağlantılı akış günlüklerinin varlığını denetler. Özellik yoksa, ilke bir akış günlüğü dağıtır. 

İlkenin tam tanımını görmek isterseniz, [tanımları sekmesini](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) ziyaret edebilir ve ilkeyi bulmak Için "akış günlükleri" araması yapabilirsiniz. 

### <a name="assignment"></a>Atama

1. İlke ayrıntılarınızı girin

- Kapsam: abonelik ortak bir seçimdir, sizin için uygun olan bir yönetim grubu veya kaynak grubu da seçebilirsiniz.  
- İlke tanımı: "ilkeleri bulma" bölümünde gösterildiği gibi seçilmelidir.
- Atamaadı: açıklayıcı bir ad seçin 

2. İlke parametreleri Ekle 

Ağ Izleyicisi hizmeti bölgesel bir hizmettir. Bu parametreler, akış günlüklerinin dağıtılmasının ilke eyleminin yürütülmesini sağlar. 
- NSG bölgesi: ilkenin hedeflediği Azure bölgeleri
- Depolama KIMLIĞI: depolama hesabının tam kaynak KIMLIĞI. Note: Bu depolama hesabı NSG ile aynı bölgede olmalıdır. 
- Network Izleyicileri RG: ağ Izleyicisi kaynağını içeren kaynak grubunun adı. Yeniden adlandırdıysanız varsayılan olan ' NetworkWatcherRG ' girebilirsiniz.
- Ağ Izleyicisi adı: Bölgesel Ağ İzleyicisi hizmetinin adı. Biçim: NetworkWatcher_RegionName. Örnek: NetworkWatcher_centralus. Tam listeye bakın.

![DINE Ilkesi parametreleri](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Düzeltme ayrıntıları ekleme

- İlkenin mevcut kaynakları etkilemesini istiyorsanız "Düzeltme görevi oluşturma" onay işaretine bakın 
- "Yönetilen kimlik oluşturma" önceden işaretlenmiş olmalıdır
- Yönetilen Kimliğiniz için öncekiyle aynı konum seçildi 
- Bu ilkeyi kullanmak için katkıda bulunan veya sahip izinlerinizin olması gerekir. Bu izinleriniz varsa herhangi bir hata görmemelisiniz.

![DINE Ilkesi düzeltmesi](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. "Gözden geçir + oluştur" seçeneğine tıklayarak atamalarınızı gözden geçirin ve aşağıdaki ekran görüntüsüne benzer bir şey görmeniz gerekir.

![DINE Ilkesi incelemesi](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Sonuçlar

Sonuçları denetlemek için, Uyumluluk sekmesini açın ve atamalarınızın adını arayın.
İlkenize bir kez aşağıdaki ekran görüntüsüne benzer bir şey görmeniz gerekir. İlkenizin çalıştırılmadığı durumlarda bir süre bekleyin.

![DINE Ilkesi sonuçları](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Sonraki adımlar 

-   Bu [öğreticiyi](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) kullanın, akış günlüklerini dağıtmak ve trafık ANALIZI için ARM şablonları kullanarak daha ayrıntılı bir şekilde devam edin.
-   [Ağ İzleyicisi](./index.yml) hakkında daha fazla bilgi